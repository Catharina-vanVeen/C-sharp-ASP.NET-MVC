# Vertigo Theater

Summary

<h2>User Stories</h2>
<ul>
  <li><a href="#7646">Subscribe</a></li>
  <li><a href="#7298">Add Production Photo Icon</a></li>
  <li><a href="#6612">Rotate Production Photos</a></li>
  <li><a href="#2">XXX</a></li>
  <li><a href="#3">XXX</a></li>
  <li><a href="#4">XXX</a></li>
</ul>



<h3 id="7646">Subscribe</h3>
<h4>Description</h4>
<p>Create a View in the Home folder called 'Subscribe'.  This page should have a form that allows the User to choose a Subscription plan.  There are 3 subscription plans to choose from, check the SubscriptionPlan model and database.  On the Subscribe page, display the 3 subscription plans.  If the User is not logged in, provide form fields for the User to enter in their Username, First Name, Last Name, etc. (All of the fields on the Register page should be here).</p>
<p>When the User is not logged in or is logged in with the role of "User", have a button in the nav bar appear with the text "Subscribe!" that takes the User to this page.  This button should not appear for Members, Admins, or any other UserRole in the future that is added.</p>
<p>When the form is submitted and the User was not signed in, the ApplicationUser and Subscriber objects should be created and stored in the database.  If the User was logged in and had a role of 'User', create a new Subscriber object for the User and be sure to change the role of the User to 'Subscriber'.  In both cases, the Subscriber object should have the SubscriptionPlan that was selected.</p>
<h4>Implementation</h4>
<p>When user is not logged in he is redirected to login or register page with a return URL to the subscribe page as to not duplicate the login/register code.</p>
<p>Create subscribe view</p>
<p>Create subscribe GET and POST methods. The POST method needs to update the UserRole linking table and the user table, since the role is currently logged in two distinct ways. (Submitted a improvement suggestion for this) The POST method needs to add teh subscriber to the subscriber table. Then the method needs to automatically log the user in again after saving to the database to update the login information and display the appropriate content for a subscriber. </p>
<p>Update register method and view to allow for return url. Add extra parameter (return url) to register POST and GET methods. Modify the method to redirect to return url. Update Register view to pass return url to the controller.</p>
<h4>Code</h4>
<pre><code>
     @model TheatreCMS.Areas.Subscribers.Models.Subscriber


    @{
        ViewBag.Title = "Subscribe";
    }

    <br/>
    <h2>Subscribe</h2>

    @if (User == null || !User.Identity.IsAuthenticated)
    {

        <p class="text-center">We offer the following subscription plans:</p>

        foreach (var plan in ViewBag.SubscriptionPlans)
        {
            <div class="subscriptionbox">
                <label class="subscriptionboxheader">@plan.SubscriptionLevel: </label>
                <p>@plan.NumberOfShows tickets for $@plan.PricePerYear per year </p>
            </div>
        }

        <p class="text-center">In order to sign up for our subscription plans, please @Html.ActionLink("Register", "Register", new { area = "", controller = "Account", returnUrl = "/Home/Subscribe" }) or @Html.ActionLink("Log in", "Login", new { area = "", controller = "Account", returnUrl = "/Home/Subscribe" })</p>

        <div class="center">
            @Html.ActionLink("Register", "Register", new { area = "", controller = "Account", returnUrl = "/Home/Subscribe" }, htmlAttributes: new { @class = "btn btn-main registerButton w-25 mb-5", @role = "button" })
            @Html.ActionLink("Login", "Login", new { area = "", controller = "Account", returnUrl = "/Home/Subscribe" }, htmlAttributes: new { @class = "btn btn-main registerButton w-25 mb-5", @role = "button" })
        </div>

      }

    else if (User.Identity.IsAuthenticated && User.IsInRole("Subscriber"))
    {
        < p > You are already registered as a subscriber </ p >
    }

    else if (User.Identity.IsAuthenticated && User.IsInRole("User"))
    {

        using (Html.BeginForm("SubscribeAsync", "Home", null, FormMethod.Post))
        {
            @Html.AntiForgeryToken()

            <div class="form-horizontal ">

                @Html.ValidationSummary(true, "", new { @class = "text-danger" })

                <p class="text-center">Please select one of the following subscription plans:</p>
                <div class="form-group">

                    @foreach (var plan in ViewBag.SubscriptionPlans)
                    {
                        string subscriptionPlanID = plan.SubscriptionPlanId.ToString();
                        <div class="subscriptionbox">
                            <label class="subscriptionboxheader">
                                <input type="radio" name="SubscriptionPlanId" value=@subscriptionPlanID> @plan.SubscriptionLevel:
                            </label>
                            <p>@plan.NumberOfShows tickets for $@plan.PricePerYear per year </p>
                        </div>
                    }

                </div>

                <div class="form-group text-center">
                    <div>
                        @Html.LabelFor(model => model.Newsletter, htmlAttributes: new { @class = "control-label" })
                        <div class="checkbox text-center">
                            @Html.EditorFor(model => model.Newsletter)
                            @Html.ValidationMessageFor(model => model.Newsletter, "", new { @class = "text-danger" })
                        </div>
                    </div>
                </div>

                <div class="form-group">
                    <div class="center">
                        <button type="submit" class="btn btn-main registerButton w-25 mb-5" value="Subscribe">Subscribe</button>
                    </div>
                </div>

            </div>
        }
    }



    @section Scripts
    {
        @Scripts.Render("~/bundles/jqueryval")
    }



</code></pre>
<pre><code>
        public ActionResult Subscribe()
        {

            ViewBag.SubscriptionPlans = db.SubscriptionPlan.OrderByDescending(p =&gt p.NumberOfShows ).ToList();

            return View();
        }


        // POST: Subscribe
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async System.Threading.Tasks.Task&ltActionResult&gt SubscribeAsync([Bind(Include = "Newsletter")] Subscriber subscriber)
        {
            string userId = User.Identity.GetUserId();
            subscriber.SubscriberId = userId;
            int plan = Convert.ToInt32(Request.Form["SubscriptionPlan"]);
            subscriber.SubscriptionPlan = db.SubscriptionPlan.Find(plan);
            ModelState.Remove("SubscriberPerson");
            subscriber.SubscriberPerson = db.Users.Find(userId);
            subscriber.CurrentSubscriber = true;

            if (ModelState.IsValid)
            {
                var userManager = new UserManager&ltApplicationUser&gt(new UserStore&ltApplicationUser&gt(db));

                //UserRoles Table: get all user's roles, and remove them, then add new role of subscriber
                var roles = await userManager.GetRolesAsync(userId);
                await userManager.RemoveFromRolesAsync(userId, roles.ToArray());
                await userManager.AddToRoleAsync(userId, "Subscriber");

                // get user from Users table and update role
                var user = (ApplicationUser)userManager.FindById(userId);
                user.Role = "Subscriber";
                userManager.Update(user);

                db.Subscribers.Add(subscriber);
                db.SaveChanges();

                //log the user in again to update role in login session
                var identity = await userManager.CreateIdentityAsync(user, DefaultAuthenticationTypes.ApplicationCookie);
                HttpContext.GetOwinContext().Authentication.SignIn(new AuthenticationProperties { IsPersistent = true }, identity);

                return RedirectToAction("Index");
            }
            ViewBag.SubscriptionPlans = db.SubscriptionPlan.OrderByDescending(p =&gt p.NumberOfShows ).ToList();
            return View("Subscribe");
        }

</code></pre>


<h3 id="7298">Add Production Photo Icon</h3>
<h4>Description</h4>
<p>We want to easily add production photos to productions in the ProductionPhotos index page.  To accomplish this, we want to display all productions on that page (not just productions with associated photos like it is now.  If there is a Production with no ProductionPhotos, that section will not appear on the ProductionPhotos Index page). Then we want to have a plus sign icon (using font awesome) associated with it, that will link to the ProductionPhotos/Create/[id] page for that particular production. When hovered over, that icon should display a message saying "Click to add a photo to this production".</p>
<p>The Productions with ProductionPhotos should also have the icon.  If there are no associated photos, put it to the right of the title.  The icon should display to the right of the last picture for each production if the Production does have photos.</p>

<h4>Implementation</h4>
<p>Change ProductionPhotosController to pass all Productions (including ProductionPhotos) instead of just the ProductionPhotos.
Edit index.cshtml to reflect the change in controller. Now the page displays Productions for which there are no photos.</p>
<p>Add 'Add Production Photo'-button to each photo in the view. </p>
<p>Edit the JQuery search function so that the 'Add Production Photo' button is displayed with the last photo only and if there are no photos, then the plus icon is displayed with the productions title.</p>

<h4>Code</h4>
<pre><code>
    public class ProductionPhotosController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        // GET: ProductionPhotos
        public ActionResult Index()
        {
            var filteredList = db.Productions.OrderByDescending(i => i.Season).ThenBy(i => i.OpeningDay).Select(i => new SelectListItem
            {
                Value = i.ProductionId.ToString(),
                Text = i.Title + " (" + i.Season + ")"
            });
            ViewData["ProductionList"] = new SelectList(filteredList, "Value", "Text");

            return View(db.Productions.Include(i => i.ProductionPhotos).OrderByDescending(i => i.Season).ThenBy(i => i.OpeningDay).ToList());
        }
</code></pre>
<pre><code>
&ltdiv class="prodphoto-content my-3 container"&gt
    @*sort by production season in descending order , then by opening day in ascending order*@
    @foreach (var production in Model)
    {
    &ltdiv id="@production.ProductionId-prodphoto-show" class="prodphoto-cards"&gt
        &lth3 class="prodphoto-prod-title text-center my-3"&gt
            @production.Title &ltspan class="badge badge-pill badge-light"&gt@production.Season&lt/span&gt
            @*This link will be hidden if there are production photos to be shown*@
            &lta href="@Url.Action("Create" , new { id = production.ProductionId })" class="fa-stack fa-1x createNewIcon production-plus-icon" data-toggle="tooltip" data-placement="bottom" title="Click to add a photo to this production"&gt
                &lti class="fa fa-circle fa-stack-1x productionPhotoCreateNewIconBackground fa-inverse"&gt&lt/i&gt
                &lti class="fa fa-plus-circle fa-stack-1x productionPhotoCreateNewIconForground"&gt&lt/i&gt
            &lt/a&gt
        &lt/h3&gt

        @if (production.ProductionPhotos.Count == 0)
        {
            &lth4 class="text-center py-3"&gt"This production does not have any photos yet."&lt/h4&gt
        }
        else
        {
            &ltdiv class="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-lg-4 row-cols-xl-4 justify-content-center"&gt
                @foreach (var photo in production.ProductionPhotos)
                {
                    &ltdiv class="col mb-4 card-and-plus-container"&gt
                        &ltdiv class="row h-100"&gt
                            &ltdiv class="col col-10 pr-0"&gt
                                &ltdiv class="card h-100"&gt
                                    &ltimg class="card-img-top" src="@Url.Action("DisplayPhoto", "Photo", new { id = photo.PhotoId })" alt="Production Photo"&gt
                                    &ltdiv class="card-body"&gt
                                        &lth5 class="card-title"&gt@Html.DisplayFor(modelItem =&gt production.Title)&lt/h5&gt
                                        &ltp class="card-text text-secondary"&gt@TextHelper.LimitCharacterCount(photo.Description, 50, true)&lt/p&gt
                                    &lt/div&gt
                                    &ltdiv class="card-footer"&gt
                                        @Html.ActionLink("Edit", "Edit", new { id = photo.ProPhotoId }, new { @class = "badge badge-pill badge-danger" }) |
                                        @Html.ActionLink("Details", "Details", new { id = photo.ProPhotoId }, new { @class = "badge badge-pill badge-danger" }) |
                                        @Html.ActionLink("Delete", "Delete", new { id = photo.ProPhotoId }, new { @class = "badge badge-pill badge-danger" })
                                    &lt/div&gt
                                &lt/div&gt
                            &lt/div&gt
                            &ltdiv class="col col-2"&gt
                                @*This plus container will be hidden unless it is the last photo to show*@
                                &ltdiv class="plus-container"&gt
                                    &lta href="@Url.Action("Create" , new { id = production.ProductionId })" class="fa-stack fa-1x createNewIcon" data-toggle="tooltip" data-placement="bottom" title="Click to add a photo to this production"&gt
                                        &lti class="fa fa-circle fa-stack-1x productionPhotoCreateNewIconBackground fa-inverse"&gt&lt/i&gt
                                        &lti class="fa fa-plus-circle fa-stack-1x productionPhotoCreateNewIconForground"&gt&lt/i&gt
                                    &lt/a&gt
                                &lt/div&gt
                            &lt/div&gt
                        &lt/div&gt
                    &lt/div&gt
                }
            &lt/div&gt
        }
        &ltbr /&gt&ltbr /&gt
    &lt/div&gt
    }
&lt/div&gt
</code></pre>
<pre><code>
//search through card-body-s for user-input and display only those cards and production titles
    function searchF() {
        var value = $(".prodphoto-searchbar").val().toLowerCase();
        $(".prodphoto-cards").hide();

        $(".prodphoto-cards").each(function () {
            $(this).find(".production-plus-icon").show()//show plus icon of production, it will be hidden if there is a photo to show
            $(".card-and-plus-container", this).each(function () {
                var status = $(this).find(".card-body").text().toLowerCase().indexOf(value) &gt -1;
                $(this).toggle(status);
                if (status) {
                    $(this).parents(".prodphoto-cards").find(".plus-container").hide(); //Hide all photo plus-icons of this production
                    $(this).find(".plus-container").show(); //show plus icon of current (which will eventually be last) photo
                    $(this).parents(".prodphoto-cards").show(); //displays the production section of the filtered cards
                    $(this).parents(".prodphoto-cards").find(".production-plus-icon").hide()//hide plus icon of production
                }
            });
        });
        
        //If there is no search term, then show all production cards, even if they do not have photos.
        if (value == "") {
            $(".prodphoto-cards").show();
        }
    };
</code></pre>







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
<pre><code>
XXX
</code></pre>
<pre><code>
XXX
</code></pre>
<pre><code>
XXX
</code></pre>
