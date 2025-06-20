# Local development of trading strategies

## Using a Conda Environment

The Quantiacs library (QNT) is optimized for local strategy development. We recommend using **Conda** for its stability
and ease of managing dependencies.

### Setup Instructions

You can follow these easy steps and create an isolated environment on your machine using **conda** for managing
dependencies and avoiding conflicts:

1. **Install Anaconda**: Download and install Anaconda
   from [Anaconda's official site](https://www.anaconda.com/products/individual).

   *Tip: Anaconda comes pre-loaded with all the packages. If you want a smaller footprint on your system, you can
   use [Miniconda](https://docs.conda.io/en/latest/miniconda.html) instead.*

2. **Create a QNT Development Environment**:
    - Open your terminal and run:
      ```bash
      conda create -n qntdev -c conda-forge 'python>=3.11,<3.14' 'ipywidgets=8.1.7' 'nbformat>=5.10.4' ipykernel ta-lib 'quantiacs-source::qnt' dash
      conda activate qntdev
      ```
    - *Optional*: Prevent auto-activation of this environment:
      ```bash
      conda config --set auto_activate_base false
      ```

3. **API Key Configuration**:
    - Retrieve your API key from your [Quantiacs profile](https://quantiacs.com/personalpage/homepage).

   ![key](./pictures/key.png)

    - Set the API key in your environment:
       ```bash
       conda env config vars set -n qntdev API_KEY={your_api_key_here}
       ```
    - Alternatively, set the API key in your code (useful for IDE compatibility issues):
      ```python
      import os
      os.environ['API_KEY'] = "{your_api_key_here}"
      ```
4. **Using the Environment**:
    - Activate the environment with:
     ```bash
   conda activate qntdev
   ```
    - Deactivate when done using:
   ```bash
   conda deactivate
   ```
    - Always reactivate when returning to development.


5. **Strategy Development**:
    - Develop in your preferred IDE.
    - For Jupyter notebook usage:
      ```bash
      jupyter notebook
      ```


6. **Contest Participation**:
    - Develop and test your strategy, then submit it to Quantiacs contests.

### Updating the conda environment

- Regularly update the QNT library for the latest features and fixes:

```bash
    ## you can remove the old one before that
    # conda remove -n qntdev quantiacs-source::qnt

    conda install -n qntdev quantiacs-source::qnt
```

You can see the library updates [here](https://anaconda.org/quantiacs-source/qnt/files).

## Pip Environment

> Note: While Conda is recommended, Pip can also be used, especially if Conda is not an option.

This one-liner combines the installation of Python, creation of a virtual environment, and installation of necessary
libraries.

### Single Command Setup

1. **One Command Setup**:
    - Ensure you have [`pyenv`](https://github.com/pyenv/pyenv)
      and [`pyenv-virtualenv`](https://github.com/pyenv/pyenv-virtualenv) installed.
    - Run the following command in your terminal:
      ```bash
      pyenv install 3.11.13 && \
      pyenv virtualenv 3.11.13 name_of_environment && \
      pyenv local name_of_environment && \
      python -m pip install 'ipywidgets==8.1.7' 'nbformat>=5.10.4' dash ipykernel git+https://github.com/quantiacs/toolbox.git
      ```

   This command will:
    - Install Python 3.10.13.
    - Create a virtual environment named `name_of_environment`.
    - Activate the environment for the current directory.
    - Install the Quantiacs toolbox and other necessary Python libraries.

2. **TA-Lib Installation**:
    - The [TA-Lib library](https://github.com/TA-Lib/ta-lib-python) may need to be installed separately due to its
      specific installation requirements.

3. **Setting the API Key**:
    - Set the Quantiacs API key in your code:
      ```python
      import os
      os.environ['API_KEY'] = "{your_api_key_here}"
      ```
    - Replace `{your_api_key_here}` with the actual API key found in
      your [Quantiacs profile](https://quantiacs.com/personalpage/homepage).

### Updating the pip environment

Use this command in your environment to install the latest version from the git repository:

```
python -m pip install --upgrade git+https://github.com/quantiacs/toolbox.git
```

## Google Colab support

If you want to use Google Colab with a hosted runtime, start with this [notebook](../_static/colab.ipynb).

This notebook contains the necessary commands to configure a hosted runtime.

If you use colab with a local runtime, then you can use regular conda environment. Go to the head of this page and
follow the instructions for conda.

## Working example Jupyter Notebook or Jupyter Lab

You can open any strategy on [Quantiacs](https://quantiacs.com) and check dependencies.

1. **Launch Jupyter**: Start Jupyter Notebook or Jupyter Lab.

2. **Open a Strategy File**: Load any `.ipynb` strategy file.

3. **List Dependencies**: Execute `!conda list` in a notebook cell to display installed packages.

4. **Review Dependencies**: Ensure all required dependencies for your strategy are present and up-to-date.

This quick check aids in maintaining a robust environment for your Quantiacs trading strategies.

## How to Check the qnt Library

Using the Quantiacs (qnt) library involves a few basic steps to create and run a trading strategy. Here's a guide on how
to do it:

### Step 1: Create a Strategy

The first step in utilizing the qnt library is to define your trading strategy. An example of a simple strategy is
provided below in the `strategy.py` file. This example demonstrates a basic long-short trading strategy based on the
crossing of two simple moving averages (SMAs) with lookback periods of 20 and 200 trading days.

### Example Strategy: Simple Moving Average Crossover

The following Python code illustrates a straightforward implementation of a trading strategy using the qnt library:

```python
# import os
# 
# os.environ['API_KEY'] = "{your_api_key_here}"

import qnt.ta as qnta
import qnt.data as qndata
import qnt.backtester as qnbk
import xarray as xr


def load_data(period):
    data = qndata.futures_load_data(tail=period)
    return data


def strategy(data):
    close = data.sel(field='close')
    sma200 = qnta.sma(close, 200).isel(time=-1)
    sma20 = qnta.sma(close, 20).isel(time=-1)
    return xr.where(sma200 < sma20, 1, -1)


qnbk.backtest(
    competition_type="futures",
    load_data=load_data,
    lookback_period=365,
    test_period=2 * 365,
    strategy=strategy
)

```

### Step 2: Run the Strategy Using the Command

After creating your strategy, the next step is to run it using the Python command line. To execute your strategy, you
can use the following command in your Python environment:

```bash
python strategy.py
```

This command runs the strategy.py script, which contains the defined trading strategy and invokes the backtest function
from the qnt library. It's important to ensure that the Python environment where you run this command has the qnt
library installed and is properly set up to access market data.

**Executing and submitting your strategy:**

1. When you finish with developing your strategy, you need to upload your code in the **Jupyter Notebook environment on
   the Quantiacs webpage.** There are 2 options:

   a) Copy and paste your code inside the cell of a Jupyter Notebook:

   ![nb](./pictures/notebook.png)

   b) Upload your python file (for example, **strategy.py**) in your Jupyter environment root directory and type in
   **strategy.ipynb**:

        import strategy

   > Place the installation commands for external dependencies to init.ipynb.

2. Run all cells to test your strategy in the Jupyter Notebook. Fix the errors if it is necessary. It is a good idea to
   run the file **precheck.ipynb**.

3. Send your strategy to the Contest from the **Development** area on your home page by clicking on the **Submit**
   button:

   ![submit](./pictures/submit.png)
