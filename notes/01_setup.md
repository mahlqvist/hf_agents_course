# Basic Setup

Create a virtual environment to install the necessary python packages. To make the virtual environment available as a kernel in Jupyter Notebook, install **ipykernel** which provides the IPython kernel.

**IPython** is an interactive command-line terminal for Python, and **IPython kernel** is the Python execution backend for Jupyter Notebook.

```bash
python -m venv myenv

pip install ipykernel

# add your virtual env to Jupyter, replace myenv with the name of your venv
python -m ipykernel install --user --name=myenv
```

This should output the path to the folder where you will find the `kernel.json` file.

```ini
Installed kernelspec myenv in /home/user/.local/share/jupyter/kernels/myenv
```

On Windows the path will look like: 

```ini
C:\Users\username\AppData\Roaming\jupyter\kernels\myenv
```

```bash
# to list the kernels
jupyter kernelspec list

# to uninstall the kernel
jupyter kernelspec uninstall myenv
```

Start Jupyter Notebook and select the virtual environment kernel. Once Jupyter Notebook opens in your web browser, create or open a notebook. In the notebook interface, go to the **Kernel** menu and select **Change kernel**. From the list of available kernels, choose the one corresponding to your virtual environment (e.g., myenv).

`!pip install` is used within a **Jupyter Notebook** or **IPython** shell, where the exclamation mark `!` is a special syntax that allows running shell commands directly from the notebook or shell.

> Remember to activate the virtual environment and start Jupyter Notebook with the activated environment each time you want to work with it