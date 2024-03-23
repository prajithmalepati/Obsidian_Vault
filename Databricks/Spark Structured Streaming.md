
- used to load streaming data
- have to use `readStream` instead of `read` in `spark.read.format()`.
- need schema while reading. can infer schema or we have to explicitly define and add it to the read command. inferring might deter performance
- when reading a stream of data, we select a folder path instead of a file in `.load()`.
- 