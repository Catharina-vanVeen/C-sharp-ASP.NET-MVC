# Vertigo Theater

Summary

<h2>User Stories</h2>
<ul>
  <li><a href="#6612">Rotate Production Photos</a></li>
  <li><a href="#2">XXX</a></li>
  <li><a href="#3">XXX</a></li>
  <li><a href="#4">XXX</a></li>
</ul>

<h3 id="6612">Rotate Production Photos</h3>

<h4>Description</h4>
<p>Right now, the Admin cannot specifically choose what Production Photo gets shown for each Production; The Production Photos are rotated in a carousel.  We want to give the Admin the ability to set what Production Photo gets shown on the Home Page for each Production but also keep the previous implementation of showing random Production Photos.  Create a new Boolean field in the Production Model called RotateProductionPhotos.  If this field is True and the Production is currently showing (on the homepage), the carousel for that Production on the homepage should behave like normal.  If the field is false, only display the DefaultPhoto for that Production; the carousel should not rotate between ProductionPhotos.</p>

<p>Add a check box on the Create and Edit page to allow the Admin to change the RotateProductionPhotos property.  Ensure that that property correctly saves to the Production when a Production is saved or edited.</p>

<p>Show new property only to Admins on Details and Index pages.</p>

<h4>Implementation</h4>
<p>I added the property RotateProductionPhotos to the model.</p>
<p>I modified the controller to pass only the default ProductionPhoto for the view to use if RotateProductionPhotos was false, and all ProductionPhotos if RotateProductionPhotos is true. No edits were therefor needed in the carousel, because the controller controls the number of pictures.</p>
<p>I modified the view to show the edit RotateProductionPhotos and to show the RotateProductionPhotos pill badge to admins only.</p>

<h4>Code</h4>
<pre><code>
            foreach (Production production in orderedProductions)
            {
                var photoArray = new List&ltProductionPhotos&gt();
                if  (production.RotateProductionPhotos)
                {
                    photoArray = db.ProductionPhotos.Where(p =&gt p.Production.ProductionId == production.ProductionId).ToList();
                }
                else
                {
                    if (production.DefaultPhoto != null)
                    {
                        photoArray.Add(production.DefaultPhoto);
                    }
                }
                productionPhotosList.Add(ShufflePhotos(photoArray));
            }

</code></pre>
<pre><code>
                    @if (User.IsInRole("Admin"))
                    {
                        &ltdt class="col"&gt
                            @Html.DisplayNameFor(model =&gt model.RotateProductionPhotos)
                        &lt/dt&gt
                        &ltdd class="col"&gt
                            @Html.DisplayFor(model =&gt model.RotateProductionPhotos)
                        &lt/dd&gt
                    }

</code></pre>
___
<pre><code>
                @* Pill badge displayed when RotateProductionPhotos is true and User is admin.*@
                @if (User.IsInRole("Admin"))
                {
                    if (item.RotateProductionPhotos)
                    {
                        &ltspan class="badge badge-pill badge-secondary"&gtRotate Images&lt/span&gt
                    }
                    else
                    {
                        &ltspan class="badge badge-pill badge-secondary"&gtDo Not Rotate Images&lt/span&gt
                    }
                }

</code></pre>

<h3 id="1">XXX</h3>
<h4>Description</h4>
<p></p>
<p></p>
<p></p>
<h4>Implementation</h4>
<p></p>
<p></p>
<p></p>
<h4>Code</h4>
<pre>
</pre>
<pre>
</pre>
<pre>
</pre>
