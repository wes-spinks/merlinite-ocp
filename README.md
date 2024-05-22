# CIA [ibm-granite/](https://huggingface.co/ibm-granite) model templates for OpenShift AI deployment

## Goal
- [ ] [IDE Code Assist via Continue.dev](https://docs.continue.dev/how-to-use-continue)

***
### work in progress
## [Granite]
OpenShift admins can use this repo to deploy the resources needed to serve models:
```
oc login --token=sha256~<token> --server=https://api.cluster.az.openshiftapps.com:6443
oc project <your project namespace>
git clone git@github.com:wes-spinks/merlinite-ocp.git; cd merlinite-ocp
git checkout granite-20b-code && oc apply -f .
oc logs -f $(oc get pod -l app=granite-20-lcpp-py -o jsonpath='{.items[0].metadata.name}')
# once the pod has spun up:
curl -X 'POST' \
  'https://<YOUR_ROUTE>.com/v1/chat/completions' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "model": "granite-20b-code-instruct",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful developer coding assistant."
    },
    {
      "role": "user",
      "content": "What does the Python requests library do?"
    }
  ]
}'
```

- [granite-20b-code-instruct-GGUF](https://huggingface.co/ibm-granite/granite-20b-code-instruct-GGUF)
   - llama-cpp-python / llama.cpp and a quantized version of IBM Granite. Currently relies on a custom [stewhite/llama-py](https://quay.io/stewhite/llama-py) image build until more than one open source communities release updates, at which point tools like Podman Desktop AI Labs, Ollama, etc will be able to support these larger models in addition to (hopefully) the smaller models soon™️
- [granite-8b-code-instruct](https://huggingface.co/ibm-granite/granite-8b-code-instruct)
   - Hugging Face TGI and raw safetensor files.

***
## Continue.dev

Once installed, add the following to your continue.dev config.json to try out the above model(s):
### granite-20b-code-instruct-GGUF (recommended)
```
    {
      "title": "My RHOAI Model",
      "model": "granite-20b-code-instruct",
      "apiBase": "https://<YOUR ROUTE>.com/v1",
      "provider": "openai",
      "contextLength": 1024,
      "template": "chatml",
      "completionOptions": {
        "maxTokens": 256
      },
      "useLegacyCompletionsEndpoint": false
    }
```
   
### granite-8b-code-instruct (not recommeneded)
```
    {
      "title": "Granite 8",
      "model": "granite-8b-code-instruct",
      "apiBase": "https://<YOUR ROUTE>.com",
      "provider": "huggingface-tgi",
      "contextLength": 1024,
      "template": "chatml",
      "completionOptions": {
          "maxTokens": 64
        },
      "useLegacyCompletionsEndpoint": false
    }
```
