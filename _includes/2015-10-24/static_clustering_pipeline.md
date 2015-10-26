
#### Initial Data
We begin with 172 days of GDELT data between 2013-04-01 and 2013-09-30. Each day has roughly 200,000 news events. Each news event is encoded with 58 attributes. These include information such as the event's date, actors, location, magnitude, and some semantic meaning. 

#### Preprocessing
We preprocess each day's file, reducing the 58 columns to 19, which we divide into four types of features: numeric, categorical, string, and importance. Some numeric features are 'DaysSincePublished' and 'GoldsteinScale', which is a -10 to 10 scale of the 'weight' of the event. Categorical data includes the CAMEO encodings for high-level event attributes, such as whether an event is aggressive in nature and in what ways it might be so. The two actor names are encoded as strings, eg 'United States' and 'Canada'. Lastly, importance features, like the number of news sources or number of mentions an event has, measure the magnitude of each event.\\

The preprocessing step lowercases all strings, drops stop words from actor names, and fills in missing attributes with indicators.

#### Feature Expansion
Part of the difficulty of this dataset is its size. In order to encode categorical and string relationships, we use a feature expansion step to one-hot encode categorical data and word to vec to encode string data.

##### One-Hot Encoding
We have seven categorical fields: 'CAMEOCode1', 'CAMEOCodeFull', 'Actor1Country', 'Actor2Country', 'Actor1GeoType', 'Actor2GeoType', and 'ActionGeoType'. These are each one-hot encoded, expanding each field to a vector of size equal to the number of classes that field has. For example, there are 275 countries in the GDELT data, so Actor1Country is expanded to a length 275 vector. In total we introduce 935 one-hot encoded fields.

##### Word to Vec
For actor names, we train multiple dictionaries on the Brown corpus to detect unigrams, bigrams, trigrams, and quadgrams. As we preprocessed all strings to remove stop words, we also remove stop words from these models. We then use the relevant model to generate a length 100 vector that encodes relationships between words, created through relative co-occurance frequencies in the Brown corpus. 

#### Clustering

#### Generalized Linear Model