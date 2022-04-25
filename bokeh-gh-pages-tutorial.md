# Visualizing and presenting your data in Python with Bokeh and GitHub Pages

## Energy Hackathon 2022

*by Ildar Akhmetov <ildar@ualberta.ca>*

### Part 1. Using Bokeh to draw and save an interactive chart

Open [Google Colab](https://colab.research.google.com/) and create a new notebook.

Alternatively, feel free to use any other Jupyter Notebook platform of your choice. In this case, make sure that you have `numpy`, `pandas` and `bokeh` packages installed.

First, let's read the data. We'll be using the [Data on Energy by Our World in Data](https://github.com/owid/energy-data) dataset.

```python
df = pd.read_csv('https://nyc3.digitaloceanspaces.com/owid-public/data/energy/owid-energy-data.csv')
```

Let's leave only what we'll need.

```python
df = df[df.country == 'Canada']
df = df[['year', 'coal_prod_per_capita']]
```

Now, we'll create a `ColumnDataSource` based on the dataframe we have. It's a specific Bokeh object. Yes, Bokeh can access Pandas dataframes directly, but we'll need to have a `ColumnDataSource` later to set up a hover tooltip.

```python
src = ColumnDataSource(data=df)
```

Now, let's create an empty Bokeh plot.

```python
p = figure(plot_width=800, plot_height=300)
```

Next, we'll add a line plot. Note how we reference `src` which is an object of type `ColumnDataSource`. For the **x** axis, we'll use `year`, and for the **y** axis, we'll use `coal_prod_per_capita`.

```python
p_coal = p.line(x = 'year', y = 'coal_prod_per_capita', source = src, color = 'red', line_width = 6)
```

We want to display the plot right here, in the notebook.

```python
output_notebook()
```

Displaying the plot.

```python
show(p)
```

Adding the title and some labels.

```python
p.title = "Production of coal in Canada"
p.xaxis.axis_label = 'Year'
p.yaxis.axis_label = 'Per capita production, measured in kilowatt-hours'
```

Bokeh uses **layouts** to enhance plots. Let's try one of them, `BoxAnnotation`, to hightlight a portion of the chart.

```python
box = BoxAnnotation(left=1950, right=1980, fill_alpha=0.2, fill_color="#F0E442")
p.add_layout(box)
show(p)
```

**Tools** work almost like layouts, providing some extra functionality. Let's use `HoverTool`, to add a nice interactive tooltip to our plot. In the `tooltips` argument, we provide the contents for the tooltip as a list of tuples. 

```python
hover = HoverTool(
        tooltips=[
            ("Year", "@year"),   
            ("Coal production", "@coal_prod_per_capita"),
        ],
        mode='vline'
    )
p.add_tools(hover)
show(p)
```

Nice! Finally, let's save the plot as a HTML file.

```python
output_file("plot.html")
save(p)
```

### Part 2. Publishing the plot with GitHub Pages

If you don't have a GitHub account, it's time to create one!

Once you're logged in, create a new repository. Choose any name, make the repository **Public**, check **Add a README file**. Other options don't matter now.

Click **Settings**, then click **Pages** at the bottom of the sidebar. 

Choose the branch **main**, it'll be the source for GitHub Pages. Click **Save**. You will see a link to your new empty website.

Upload the `plot.html` file that you downloaded previously, by clicking **Add file** --> **Upload files**. 

Finally, let's create an index page. Click **Add file** --> **Create new file**, type `index.md` as the file name.

Let's simply add a header and a hyperlink:

```markdown
# My Dashboard

[Production of coal in Canada](plot.html)
```

Click **Commit new file**.

Now, wait for a minute or so and open your webpage to see the changes.
