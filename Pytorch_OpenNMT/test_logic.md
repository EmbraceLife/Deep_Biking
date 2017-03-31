`python translate.py -model demo_model_acc_4.97_ppl_6934.21_e13.pt -src data/src-test.txt -tgt data/tgt-test.txt -replace_unk -verbose -output demo_pred.txt`

translate.py --> main():


1. get args from console, as opt
2. select between gpu and cpu device
3. create a translator with opt
4. open outputfile as outF
5. set 0 to predScoreTotal, predWordsTotal, goldScoreTotal, goldWordsTotal
6. set [] to srcBatch, tgtBatch
7. set count 0
8. open data/tgt-test.txt as tgtF
9. get each line of data/src-test.txt:
    - if line is not empty
      - split the lines into words list, as srcTokens
      - add/append srcTokens onto srcBatch
      - if tgtF is available:
        - split lines into words list, as tgtTokens
        - add/append tgtTokens onto tgtBatch
      - if num of srcBatch < required batch_size:
        - keep looping
    - if line is empty:
      - srcBatch is empty, then break the loop
    - set predBatch, predScore, goldScore to be the outputs of translator.translate(srcBatch, tgtBatch)
    - add up all the scores of each line prediction, as predScoreTotal
    - add up the number of words of each line, as predWordsTotal
    - if tgtF is available with content:
      - sum up all the goldScore of each line, as goldScoreTotal
      - sum up num of lines of tgtBatch, as predWordsTotal
    - for each of the item in predBatch:
      - count up
      - write each line of predBatch as string onto outF
      - save it in buffer not onto disk

      - if verbose required:
        - join each word of a line of srcBatch with " ", as srcSent
        - if required to be lower case:
          - do it lower case
        - print out num of line, and line original of srcBatch
        - print out num of line, and line prediction  
        - print out prediction score

        - if tgtF is available:
          - join each word of a line of tgtBatch with " ", as tgtSent
          - if required to be lower case:
            - do it
          - print Gold count and tgtSent or this tgt original line
          - print Gold score of tihs line

        - if opt.n_best > 1:
          - print best HYP
          - print num of best predScores and predBatches of each line
    - set [] for srcBatch, tgtBatch

  10. reportScore ....
