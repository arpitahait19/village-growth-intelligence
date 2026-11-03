# Village Economic Growth Intelligence

Finding the top 100 villages in India with the highest economic growth over the last 5 years, using satellite data and public datasets.



## &#x20;Data sources

See [`SOURCES.md`](SOURCES.md) for every dataset, where it came from, and why it was chosen.

## &#x20;Pipeline

|Step|Script|
|-|-|
|1. Extract|`src/01\\\_extract.py`|
|2. Clean|`src/02\\\_clean.py`|
|3. Features|`src/03\\\_features.py`|
|4. Score|`src/04\\\_score.py`|
|5. Visualise|`src/05\\\_visualise.py`|

## &#x20;How to run

```bash
git clone https://github.com/<your-username>/village-growth-intelligence.git
cd village-growth-intelligence
pip install -r requirements.txt
earthengine authenticate
# then run scripts in order:
python src/01\_extract.py
```



## &#x20;Outputs

* `outputs/top100\_villages.csv` – final ranked dataset
* `outputs/maps/top100\_map.html` – interactive map
* `presentation/` – final slides

## &#x20; 

