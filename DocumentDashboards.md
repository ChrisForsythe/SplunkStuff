# Document Your Dashboards

There's a few ways you can ensure your dashboards are documented.  All of 
them should be used.

## Show documentation panel

Create an input that is a checkbox only and just sets a token to a simple
text value, e.g. "showdocs"

    <input type="checkbox" token="showdocs">
      <label>Show documentation?</label>
      <choice value="true">Yes?</choice> 
    </input>

Then, down below, create a hidden row of an HTML panel with your documentation:

    <row depends="$showdocs$">
      <panel>
        <html>
          <h1>TFM</h1>
          <p>This is The Fine Manual (TFM).  You need to Write TFM so someone
             else can Read TFM.</p>
          <p>Remember, every paragraph tag must be closed in Splunk XML.
        </html>
      </panel>
    </row>

## Inline comment your searches

The second trick is to add inline comments using the triple backticks to your
search strings

    ```
    Some comments here
    ```

## True XML comments

You can always add XML comments themselves.  Great for commenting the XML

    <!-- comments here -->

Warning, GUI changes may do odd things to XML comments.
