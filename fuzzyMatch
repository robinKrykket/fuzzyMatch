#!/usr/bin/env python3

# to use this:
#  virtualenv venv   (creates the virtual env directory / setup)
#  source venv/bin/activate  (start using the venv)
#  pip install fuzzywuzzy
#  pip install python-Levenshtein
# then you are free to run the program like this:
#    python fuzzyMatch.py  ~/Downloads/Matching\ Directory\ Combined\ -\ EP.csv 0 ~/Downloads/BankBillPayFile.csv 0

import sys
import argparse
import csv
from fuzzywuzzy import fuzz
import string

class CSVRecord:
  def __init__(self, data, name):
    self.data = data
    self.name = name


def get_dataset(file, colNum):
    # take the colNum column as the key, value is the entire row
    # this key is usually the biller name, and we want to remove numbers at the
    # end and also remove spaces from the key too
    # so it looks like:
    #      {"comcast":  ["Comcast 123", "comcast_ncp", "12334252454543"]}
    table = str.maketrans(dict.fromkeys(string.punctuation))
    with open(str(file.name), encoding="utf-8", errors="replace") as handle:
        return {row[colNum].lower().rstrip(string.digits).replace(" ", "").translate(table) : CSVRecord(row, row[colNum]) for row in csv.reader(handle)}


def main(file1, file1ColNum, file2, file2ColNum):
    file1BillerNameToCSVRow = get_dataset(file1, file1ColNum)
    file2BillerNameToCSVRow = get_dataset(file2, file2ColNum)
    # import pdb; pdb.set_trace()

    # these are the items that are an exact match
    billerNamesFile1 = set(file1BillerNameToCSVRow.keys())
    billerNamesFile2 = set(file2BillerNameToCSVRow.keys())
    matchedItems = billerNamesFile1 & billerNamesFile2
    print ("File 1 has  ", len(billerNamesFile1), "  items.")
    print ("File 2 has  ", len(billerNamesFile2), "  items.")
    print ("Great news!  We found ", len(matchedItems), " exact matches.")
    with open('matches.csv', 'w', newline='', encoding="utf-8") as csvfile:
        csvOutput = csv.writer(csvfile)
        for matchedItem in matchedItems:
            # this code will match the rows with file 2 being the first columns
            # followed by the columns in file 1
            cellValue = []
            cellValue.extend(file2BillerNameToCSVRow[matchedItem].data)
            cellValue.extend(file1BillerNameToCSVRow[matchedItem].data)
            csvOutput.writerow(cellValue)

        notMatchedInKey2 = billerNamesFile2 - billerNamesFile1
        notMatchedInKey1 = billerNamesFile1 - billerNamesFile2
        print ("Bad news :(  We found ", len(notMatchedInKey2), " in file2 that are not in file1 and we still have ", len(notMatchedInKey1), " to compare them against")
        fuzzyMatchCount = 0
        for billerName2 in notMatchedInKey2:
            matchedItems = []
            for billerName1 in notMatchedInKey1:
                score = fuzz.ratio(billerName2, billerName1)
                if score > 65:
                    matchedItems.append({
                          'score':  score,
                          'strvalue': file2BillerNameToCSVRow[billerName2].data[file2ColNum] + "=" + file1BillerNameToCSVRow[billerName1].data[file1ColNum] + " (" + str(score) + ")"})
            if len(matchedItems) > 0:
                if len(matchedItems) > 1:
                    matchedItems = sorted(matchedItems, key = lambda i: i['score'], reverse = True)
                if matchedItems[0]['score'] < 90:
                    matchedItems.insert(0, {'strvalue': ''})
                    if matchedItems[1]['score'] < 80:
                        matchedItems.insert(0, {'strvalue': ''})
                        if matchedItems[2]['score'] < 70:
                            matchedItems.insert(0, {'strvalue': ''})
                row = []
                row.extend(file2BillerNameToCSVRow[billerName2].data)
                row.extend(item['strvalue'] for item in matchedItems)
                fuzzyMatchCount += 1
                csvOutput.writerow(row)
        print ("We have ", str(fuzzyMatchCount), " fuzzy matches ")
    print ("Done, check the output file")


if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    parser.add_argument('file1', type=argparse.FileType('r'))
    parser.add_argument("file1Col", type=int, help="The column number to look at (zero based)")
    parser.add_argument('file2', type=argparse.FileType('r'))
    parser.add_argument("file2Col", type=int, help="The column number to look at (zero based)")

    args = parser.parse_args()

    main(args.file1, args.file1Col, args.file2, args.file2Col)
