```
###
# This will allow to re-run requests for {retries} times 
# with enhancing sleep between requests
# {backoff factor} * (2 ^ ({number of total retries} - 1)) 
# If the backoff_factor is 0.1, 
# then sleep() will sleep for [0.1s, 0.2s, 0.4s, 0.8s, ... ] 
###
def requests_retry_session(
  retries=5,
  backoff_factor=0.8,
  status_forcelist=(429, 500, 502, 503, 504),
  session=None,
):
  session = session or requests.Session()
  retry = Retry(
    total=retries,
    read=retries,
    connect=retries,
    backoff_factor=backoff_factor,
    status_forcelist=status_forcelist,
  )
  Retry.BACKOFF_MAX=5 # limiting predefined static param, not greater than 5 seconds
  adapter = HTTPAdapter(max_retries=retry)
  session.mount('http://', adapter)
  session.mount('https://', adapter)
  return session
# requests_retry_session().get(f"https://some.url")
```
