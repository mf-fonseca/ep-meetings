# ep-meetings

The script :

1. First fetches the list of meetings on certain years.
	- The API call for this is https://data.europarl.europa.eu/api/v2/meetings?year={0}&format=application%2Fld%2Bjson&offset=0&limit=1000, with `{0}` replaced by a year
	- Example : https://data.europarl.europa.eu/api/v2/meetings?year=2025&format=application%2Fld%2Bjson&offset=0&limit=1000

2. Then, for each meeting, retrieves the decisions taken during this meeting, and save this into a file
	- The API call for this is https://data.europarl.europa.eu/api/v2/meetings/{0}/decisions?vote-method=ROLL_CALL_EV&format=application%2Fld%2Bjson&json-layout=framed-and-included&offset=0&limit=1000, with `{0}` replaced by a meeting id, for example `MTG-PL-2025-01-20`
	- Example : https://data.europarl.europa.eu/api/v2/meetings/MTG-PL-2025-01-20/decisions?vote-method=ROLL_CALL_EV&format=application%2Fld%2Bjson&json-layout=framed-and-included&offset=0&limit=1000

3. Then, conduct the analysis on a subset of the meetings (based on a parameter in the config file), and produces the CSV file. The parsing of the JSON output of the API reads the following information:
	- Reads all vote results objects in `data[]`
	- For each vote result, reads :
		- `activity_id` : ID of the vote
		- `activity_label["en"]` : english label of the vote
		- `activity_date` : date of the vote
		- the vote results :
			- `had_voter_against` : list of MEPS identifiers that voted AGAINST
			- `had_voter_for` : list of MEPS identifiers that voted FOR
			- `had_voter_abstention` : list of MEPS identifiers that voted ABSTENTION
		- for each voter MEP, go and read the following information about the MEP in the `included` section of the JSON:
			- `identifier` : the identifier of the MEP - this is used as the join key with the vote results
			- `label` : name of the MEP
			- `api:political-group` : the political group of the MEP *at the time of the vote*