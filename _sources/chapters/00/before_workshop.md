# Before workshop 

1. Clone the openai-agents workshop project: [https://github.com/MakkOen/openai-agents-workshop](https://github.com/MakkOen/openai-agents-workshop) and navigate to `agents-demo` folder which contains the following:
    * jupyter notebook `playground.ipynb` 
    * `requirements.txt` requirements file to set up environment with everything we need
2. Use your chosen  tool of choice to set up the Python environment:
   - **conda**:
     ```bash
     cd agents-demo
     conda create --name agents-workshop python=3.9 --file requirements.txt
     conda activate agents-workshop
     ```
   - **venv**:
     ```bash
     cd agents-demo
     python -m venv --python=python3.9 agents-workshop
     source agents-workshop/bin/activate   # On Windows: agents-workshop\Scripts\activate
     pip install -r requirements.txt
     ```
   - **virtualenv**:
     ```bash
     cd agents-demo
     virtualenv --python=python3.9 agents-workshop
     source agents-workshop/bin/activate   # On Windows: agents-workshop\Scripts\activate
     pip install -r requirements.txt
     ```
3. Run the testing jupyter notebook:
    ```bash
    jupyter notebook playground.ipynb
    ```
4. Got to [https://pastebin.com/8F3RSsn0](https://pastebin.com/8F3RSsn0), password `namibia`, and copy one of the API keys to the notebook:
    ```python
    OPENAI_API_KEY = ...
    ```

## Alternative: 
Open the [google colab notebook](https://colab.research.google.com/drive/1XdMLZFUoJC_emOIfhsolJ7VrQUw3-WVa?usp=sharing) and make your own copy (`File -> Save a copy in Drive`)
