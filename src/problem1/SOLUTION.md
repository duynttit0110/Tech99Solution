```shell
cat ./transaction.txt | jq -c 'select(.symbol == "TSLA")' | while read -r line;
do curl -s "http://example.com/api/$(echo $line | jq -r .order_id)";
echo; done > "output.txt"
```

# Hint
- | : Pipelines
- cat ./transaction.txt : Read content file transaction.txt
- jq -c : return json on a single line
- jq -r : remove json format and return plain string
- read -r line : read content line by line, content is stored in $line
