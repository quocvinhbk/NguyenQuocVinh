Provide your CLI command here:
# Solution for problem 1: Too Many Things To Do

# Question:
    - What is the Order IDs that are selling `TSLA`, I don't know what is `TSLA` meaning?

- Check the content of the file `./transaction-log.txt`, I got that `TSLA` is called `symbol`, that matching with 2 order id: 12346 and 12362. We don't have any other information, so I assume that `TSLA` is `symbol`.
- So, the objective is: Submit a HTTP GET request to `https://example.com/api/:order_id` with Order IDs is 12346 and 12362
- In the http request `https://example.com/api/:order_id`, `:order_id` is a path parameter, we should replace `:order_id` with our id, that is: `12346` and `12362`. Result:
    - https://example.com/api/12346
    - https://example.com/api/12362
- We only has 1 output file (output.txt) for 2 Order IDs, that mean, those 2 requests output need to merged in to just 1 file.

# Solution:
## Option 1: Using wget
```sh
wget https://example.com/api/12346 -O ->> output.txt && wget https://example.com/api/12362 -O ->> output.txt
```

## Option 2: Using curl
```sh
curl https://example.com/api/12346 -o ->> output.txt && curl https://example.com/api/12362 -o ->> output.txt
```
