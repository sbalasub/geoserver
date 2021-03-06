Providing Metadata
==================

.. highlight:: xml

One feature that appears in SLD that has no analog in CSS is the ability to
provide *metadata* for styles and style rules.  For example, this SLD embeds
a title for its single rule::

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <StyledLayerDescriptor version="1.0.0" 
        xmlns="http://www.opengis.net/sld" 
        xmlns:ogc="http://www.opengis.net/ogc"
        xmlns:xlink="http://www.w3.org/1999/xlink" 
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xmlns:gml="http://www.opengis.net/gml"
        xsi:schemaLocation="http://www.opengis.net/sld 
            http://schemas.opengis.net/sld/1.0.0/StyledLayerDescriptor.xsd"
    >
      <NamedLayer>
        <Name>Country Borders</Name>
        <UserStyle>
          <Name>borders</Name>
          <Title>Country Borders</Title>
          <Abstract>
              Borders of countries, in an appropriately sovereign aesthetic.
          </Abstract>
          <FeatureTypeStyle>
            <Rule>
              <Title>Borders</Title>
              <LineSymbolizer>
                <Stroke>
                  <CssParameter name="stroke-width">0.2</CssParameter>
                </Stroke>
              </LineSymbolizer>
            </Rule>
         </FeatureTypeStyle>
        </UserStyle>
      </NamedLayer>
    </StyledLayerDescriptor>

.. highlight:: css

Software such as GeoServer can use this metadata to automatically generate nice
legend images directly from the style.  You don't have to give up this ability
when styling maps in CSS; just add comment *before* your rules including
lines that start with '@title' and '@abstract'.  Here is the analogous style in
CSS::

    /*
     * @title This is a point layer.
     * @abstract This is an abstract point layer.
     */
    * {
        mark: mark(circle);
    }

Rules can provide either a title, an abstract, both, or neither.  The SLD Name
for a rule is autogenerated based on the filters from the CSS rules that
combined to form it, for aid in troubleshooting.

Combined Rules
--------------

One thing to keep in mind when dealing with CSS styles is that multiple rules
may apply to the same subset of map features, especially as styles get more
complicated.  Metadata is inherited similarly to CSS properties, but metadata
fields are **combined** instead of overriding less specific rules.  That means
that when you have a style like this::

    /* @title Borders */
    * {
        stroke: black;
    }

    /* @title Parcels */
    [category='parcel'] {
        fill: blue;
    }

The legend entry for parcels will have the title ``'Parcels with Borders'``.
If you don't like this behavior, then only provide titles for the most specific
rules in your style. (Or, suggest something better in an issue report!)  Rules
that don't provide titles are simply omitted from title aggregation.
