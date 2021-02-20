# Vertigo Theater

<p>I worked with a team of colleagues on developing a full scale C# (ASP.NET MVC) web application for a theater group. The website keeps track of productions, cast members, subscribers etc. I added features, either by modifying pre-existing code or writing new code.  I also made several suggestions for improving the database structure. I worked on back-end and front-end, using C#, JavaScript, JQuery, Ajax, HTML and CSS.</p>

<p>See below for description, code and images</p>

<h2 id="features">Features</h2>
<ul>
  <li><a href="#7151">Asynchrounous Edit Button</a></li>
  <li><a href="#7390">Direct Message Fans</a></li>
  <li><a href="#6342">Mobile Calendar</a></li>
  <li><a href="#7646">Subscribe</a></li>
  <li><a href="#7298">Add Production Photo Icon</a></li>
  <li><a href="#6612">Rotate Production Photos</a></li>
  <li><a href="#6598">Admin Navigation Panel</a></li>
  <li><a href="#7858">Unique Subscription Plan</a></li>
</ul>

<h3 id="7151">Asynchrounous Edit Button</h3>
<h4>Description</h4>
<p>Create a button at the end of each table row for each Production.  This button should be a font awesome edit icon.  When clicked, the information associated with the edit button should become editable.  Also, the edit button should disappear and two more buttons should appear: a checkmark button and an 'X' button.  If the admin clicks the checkmark, the information that was edited in that field should be saved to the database asynchronously (the page should not refresh; Ajax may be needed).  If the 'X' is clicked, the information doesn't change.  For example, if the edit button at the end of the Matinee Time row is clicked, the matinee time should be editable and a green checkmark button and red 'X' button should replace the edit button.</p>
<h4>Implementation</h4>
<p>Html: Add edit button. Create Ajax forms outside table to avoid interference from table. Add hidden input fields and submit and cancel buttons. </p>
<p>Javascript: Write script to appropriately show and hide edit fields and buttons. Write script to correctly handle result of Ajax call, hide elements and display message.</p>
<p>Controller: Write POST method that validates changes, updates the database and returns the apporpriate JSON message.</p>

![Asynchrounous Edit Button](GitHub-images/7151.PNG?raw=true)
<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>

                @{String FormOpeningDay = item.ProductionId + "-OpeningDayForm";}
                @{String FieldOpeningDayMessage = item.ProductionId + "-OpeningDayMessage";}
                @{String FieldOpeningDay = item.ProductionId + "-OpeningDayField";}
                @{String EditButtonOpeningDay = item.ProductionId + "-OpeningDayEditBtn";}
                @{String CheckButtonOpeningDay = item.ProductionId + "-OpeningDayCheckBtn";}
                @{String CloseButtonOpeningDay = item.ProductionId + "-OpeningDayCloseBtn";}
                @{String ValueOpeningDay = item.ProductionId + "-OpeningDayValue";}
                
                @{String FormClosingDay = item.ProductionId + "-ClosingDayForm";}
                @{String FieldClosingDayMessage = item.ProductionId + "-ClosingDayMessage";}
                @{String FieldClosingDay = item.ProductionId + "-ClosingDayField";}
                @{String EditButtonClosingDay = item.ProductionId + "-ClosingDayEditBtn";}
                @{String CheckButtonClosingDay = item.ProductionId + "-ClosingDayCheckBtn";}
                @{String CloseButtonClosingDay = item.ProductionId + "-CloseingDayCloseBtn";}
                @{String ValueClosingDay = item.ProductionId + "-CloseingDayValue";}

                @{String FormShowtimeMat = item.ProductionId + "-ShowtimeMatForm";}
                @{String FieldShowtimeMatMessage = item.ProductionId + "-ShowtimeMatMessage";}
                @{String FieldShowtimeMat = item.ProductionId + "-ShowtimeMatField";}
                @{String EditButtonShowtimeMat = item.ProductionId + "-ShowtimeMatEditBtn";}
                @{String CheckButtonShowtimeMat = item.ProductionId + "-ShowtimeMatCheckBtn";}
                @{String CloseButtonShowtimeMat = item.ProductionId + "-ShowtimeMatCloseBtn";}
                @{String ValueShowtimeMat = item.ProductionId + "-ShowtimeMatValue";}

                @{String FormShowtimeEve = item.ProductionId + "-ShowtimeEveForm";}
                @{String FieldShowtimeEveMessage = item.ProductionId + "-ShowtimeEveMessage";}
                @{String FieldShowtimeEve = item.ProductionId + "-ShowtimeEveField";}
                @{String EditButtonShowtimeEve = item.ProductionId + "-ShowtimeEveEditBtn";}
                @{String CheckButtonShowtimeEve = item.ProductionId + "-ShowtimeEveCheckBtn";}
                @{String CloseButtonShowtimeEve = item.ProductionId + "-ShowtimeEveCloseBtn";}
                @{String ValueShowtimeEve = item.ProductionId + "-ShowtimeEveValue";}

                @if (User.IsInRole("Admin"))
                {
                    using (Ajax.BeginForm("PartialEdit", "Productions", null, new AjaxOptions { HttpMethod = "POST", OnSuccess = "success", OnFailure = "success" }, new { @id = @FormOpeningDay })) { @Html.AntiForgeryToken() }
                    using (Ajax.BeginForm("PartialEdit", "Productions", null, new AjaxOptions { HttpMethod = "POST", OnSuccess = "success" }, new { @id = @FormClosingDay })) { @Html.AntiForgeryToken() }
                    using (Ajax.BeginForm("PartialEdit", "Productions", null, new AjaxOptions { HttpMethod = "POST", OnSuccess = "success" }, new { @id = @FormShowtimeMat })) { @Html.AntiForgeryToken() }
                    using (Ajax.BeginForm("PartialEdit", "Productions", null, new AjaxOptions { HttpMethod = "POST", OnSuccess = "success" }, new { @id = @FormShowtimeEve })) { @Html.AntiForgeryToken() }
                }

                <table class="w-100">
                    <tr class="production-index-tr-styling">
                        <th class="productions-index-th-styling">
                            @Html.DisplayNameFor(model => model.OpeningDay)
                        </th>
                        <td class="productions-index-td-styling">
                            <span id="@ValueOpeningDay">@item.OpeningDay.ToShortDateString()<br /></span>
                            @Html.EditorFor(model => item.OpeningDay, new { htmlAttributes = new { @class = "form-control edit-functionality", @Name = "OpeningDay", @form = @FormOpeningDay, @id = @FieldOpeningDay } })

                            @if (User.IsInRole("Admin"))
                            {
                                <span id="@FieldOpeningDayMessage"></span>

                                @*Fields necessary for controller to work*@
                                <input type="hidden" form="@FormOpeningDay" id="propertyName" name="PropertyName" value="OpeningDay">
                                <input type="hidden" form="@FormOpeningDay" id="ProductionId" name="ProductionId" value="@item.ProductionId">

                                @*Fields necessary for the Success and Failure functions to work.*@
                                <input type="hidden" form="@FormOpeningDay" id="ProductionId" name="MessageField" value="@FieldOpeningDayMessage">
                                <input type="hidden" form="@FormOpeningDay" id="InputField" name="InputField" value="@FieldOpeningDay">
                                <input type="hidden" form="@FormOpeningDay" id="EditButton" name="EditButton" value="@EditButtonOpeningDay">
                                <input type="hidden" form="@FormOpeningDay" id="CheckButton" name="CheckButton" value="@CheckButtonOpeningDay">
                                <input type="hidden" form="@FormOpeningDay" id="CloseButton" name="CloseButton" value="@CloseButtonOpeningDay">
                                <input type="hidden" form="@FormOpeningDay" id="ValueSpan" name="ValueSpan" value="@ValueOpeningDay">
                            }
                        </td>

                        @if (User.IsInRole("Admin"))
                        {
                            <td class="productions-index-td-styling">
                                <button type="button" class="edit-btn-set" id="@EditButtonOpeningDay" onclick="show_edit_functions('@FieldOpeningDay', '@CheckButtonOpeningDay', '@CloseButtonOpeningDay', '@EditButtonOpeningDay', '@FieldOpeningDayMessage', '@ValueOpeningDay')"><i class="far fa-edit"></i></button>
                                <button type="submit" form="@FormOpeningDay" class="edit-btn-set edit-functionality" id="@CheckButtonOpeningDay"><i class="far fa-check-square"></i></button>
                                <button type="button" class="edit-btn-set edit-functionality" id="@CloseButtonOpeningDay" onclick="hide_edit_functions('@FieldOpeningDay', '@CheckButtonOpeningDay', '@CloseButtonOpeningDay', '@EditButtonOpeningDay', '@FieldOpeningDayMessage', '@ValueOpeningDay')"><i class="far fa-window-close"></i></button>
                            </td>
                        }
                    </tr>
                    <tr class="production-index-tr-styling">
                        <th class="productions-index-th-styling">
                            @Html.DisplayNameFor(model => model.ClosingDay)
                        </th>
                        <td class="productions-index-td-styling">
                            <span id="@ValueClosingDay">@item.ClosingDay.ToShortDateString()<br /></span>
                            @Html.EditorFor(model => item.ClosingDay, new { htmlAttributes = new { @class = "form-control edit-functionality", @Name = "ClosingDay", @form = @FormClosingDay, @id = @FieldClosingDay } })
                            @if (User.IsInRole("Admin"))
                            {
                                <span id="@FieldClosingDayMessage"></span>

                                @*Fields necessary for controller to work*@
                                <input type="hidden" form="@FormClosingDay" id="propertyName" name="PropertyName" value="ClosingDay">
                                <input type="hidden" form="@FormClosingDay" id="ProductionId" name="ProductionId" value="@item.ProductionId">

                                @*Fields necessary for the Success and Failure functions to work.*@
                                <input type="hidden" form="@FormClosingDay" id="ProductionId" name="MessageField" value="@FieldClosingDayMessage">
                                <input type="hidden" form="@FormClosingDay" id="InputField" name="InputField" value="@FieldClosingDay">
                                <input type="hidden" form="@FormClosingDay" id="EditButton" name="EditButton" value="@EditButtonClosingDay">
                                <input type="hidden" form="@FormClosingDay" id="CheckButton" name="CheckButton" value="@CheckButtonClosingDay">
                                <input type="hidden" form="@FormClosingDay" id="CloseButton" name="CloseButton" value="@CloseButtonClosingDay">
                                <input type="hidden" form="@FormClosingDay" id="ValueSpan" name="ValueSpan" value="@ValueClosingDay">
                            }
                        </td>

                        @if (User.IsInRole("Admin"))
                        {
                            <td class="productions-index-td-styling">
                                <button type="button" class="edit-btn-set" id="@EditButtonClosingDay" onclick="show_edit_functions('@FieldClosingDay', '@CheckButtonClosingDay', '@CloseButtonClosingDay', '@EditButtonClosingDay', '@FieldClosingDayMessage', '@ValueClosingDay')"><i class="far fa-edit"></i></button>
                                <button type="submit" form="@FormClosingDay" class="edit-btn-set edit-functionality" id="@CheckButtonClosingDay"><i class="far fa-check-square"></i></button>
                                <button type="button" class="edit-btn-set edit-functionality" id="@CloseButtonClosingDay" onclick="hide_edit_functions('@FieldClosingDay', '@CheckButtonClosingDay', '@CloseButtonClosingDay', '@EditButtonClosingDay', '@FieldClosingDayMessage', '@ValueClosingDay')"><i class="far fa-window-close"></i></button>
                            </td>
                        }
                    </tr>

                    @if (item.ShowtimeMat.HasValue || User.IsInRole("Admin"))
                    {
                        <tr class="production-index-tr-styling">
                            <th class="productions-index-th-styling">
                                @Html.DisplayNameFor(model => model.ShowtimeMat)
                            </th>
                            <td class="productions-index-td-styling">
                                <span id="@ValueShowtimeMat">@item.ShowtimeMat.GetValueOrDefault().ToShortTimeString()<br /></span>
                                @Html.EditorFor(model => item.ShowtimeMat, new { htmlAttributes = new { @class = "form-control edit-functionality", @Name = "ShowtimeMat", @form = @FormShowtimeMat, @id = @FieldShowtimeMat } })

                                @if (User.IsInRole("Admin"))
                                {
                                    <span id="@FieldShowtimeMatMessage"></span>

                                    @*Fields necessary for controller to work*@
                                    <input type="hidden" form="@FormShowtimeMat" id="propertyName" name="PropertyName" value="ShowtimeMat">
                                    <input type="hidden" form="@FormShowtimeMat" id="ProductionId" name="ProductionId" value="@item.ProductionId">

                                    @*Fields necessary for the Success and Failure functions to work.*@
                                    <input type="hidden" form="@FormShowtimeMat" id="ProductionId" name="MessageField" value="@FieldShowtimeMatMessage">
                                    <input type="hidden" form="@FormShowtimeMat" id="InputField" name="InputField" value="@FieldShowtimeMat">
                                    <input type="hidden" form="@FormShowtimeMat" id="EditButton" name="EditButton" value="@EditButtonShowtimeMat">
                                    <input type="hidden" form="@FormShowtimeMat" id="CheckButton" name="CheckButton" value="@CheckButtonShowtimeMat">
                                    <input type="hidden" form="@FormShowtimeMat" id="CloseButton" name="CloseButton" value="@CloseButtonShowtimeMat">
                                    <input type="hidden" form="@FormShowtimeMat" id="ValueSpan" name="ValueSpan" value="@ValueShowtimeMat">

                                }
                            </td>

                            @if (User.IsInRole("Admin"))
                            {
                                <td class="productions-index-td-styling">
                                    <button type="button" class="edit-btn-set" id="@EditButtonShowtimeMat" onclick="show_edit_functions('@FieldShowtimeMat', '@CheckButtonShowtimeMat', '@CloseButtonShowtimeMat', '@EditButtonShowtimeMat', '@FieldShowtimeMatMessage', '@ValueShowtimeMat')"><i class="far fa-edit"></i></button>
                                    <button type="submit" form="@FormShowtimeMat" class="edit-btn-set edit-functionality" id="@CheckButtonShowtimeMat"><i class="far fa-check-square"></i></button>
                                    <button type="button" class="edit-btn-set edit-functionality" id="@CloseButtonShowtimeMat" onclick="hide_edit_functions('@FieldShowtimeMat', '@CheckButtonShowtimeMat', '@CloseButtonShowtimeMat', '@EditButtonShowtimeMat', '@FieldShowtimeMatMessage', '@ValueShowtimeMat')"><i class="far fa-window-close"></i></button>
                                </td>
                            }
                        </tr>
                    }

                    @if (item.ShowtimeEve.HasValue || User.IsInRole("Admin"))
                    {
                        <tr class="production-index-tr-styling">
                            <th class="productions-index-th-styling">
                                @Html.DisplayNameFor(model => model.ShowtimeEve)
                            </th>
                            <td class="productions-index-td-styling">
                                <span id="@ValueShowtimeEve">@item.ShowtimeEve.GetValueOrDefault().ToShortTimeString()</span>
                                @Html.EditorFor(model => item.ShowtimeEve, new { htmlAttributes = new { @class = "form-control edit-functionality", @Name = "ShowtimeEve", @form = @FormShowtimeEve, @id = @FieldShowtimeEve } })

                                @if (User.IsInRole("Admin"))
                                {
                                    <span id="@FieldShowtimeEveMessage"></span>

                                    @*Fields necessary for controller to work*@
                                    <input type="hidden" form="@FormShowtimeEve" id="propertyName" name="PropertyName" value="ShowtimeEve">
                                    <input type="hidden" form="@FormShowtimeEve" id="ProductionId" name="ProductionId" value="@item.ProductionId">

                                    @*Fields necessary for the Success and Failure functions to work.*@
                                    <input type="hidden" form="@FormShowtimeEve" id="ProductionId" name="MessageField" value="@FieldShowtimeEveMessage">
                                    <input type="hidden" form="@FormShowtimeEve" id="InputField" name="InputField" value="@FieldShowtimeEve">
                                    <input type="hidden" form="@FormShowtimeEve" id="EditButton" name="EditButton" value="@EditButtonShowtimeEve">
                                    <input type="hidden" form="@FormShowtimeEve" id="CheckButton" name="CheckButton" value="@CheckButtonShowtimeEve">
                                    <input type="hidden" form="@FormShowtimeEve" id="CloseButton" name="CloseButton" value="@CloseButtonShowtimeEve">
                                    <input type="hidden" form="@FormShowtimeEve" id="ValueSpan" name="ValueSpan" value="@ValueShowtimeEve">
                                }
                            </td>

                            @if (User.IsInRole("Admin"))
                            {
                                <td class="productions-index-td-styling">
                                    <button type="button" class="edit-btn-set" id="@EditButtonShowtimeEve" onclick="show_edit_functions('@FieldShowtimeEve', '@CheckButtonShowtimeEve', '@CloseButtonShowtimeEve', '@EditButtonShowtimeEve', '@FieldShowtimeEveMessage', '@ValueShowtimeEve')"><i class="far fa-edit"></i></button>
                                    <button type="submit" form="@FormShowtimeEve" class="edit-btn-set edit-functionality" id="@CheckButtonShowtimeEve"><i class="far fa-check-square"></i></button>
                                    <button type="button" class="edit-btn-set edit-functionality" id="@CloseButtonShowtimeEve" onclick="hide_edit_functions('@FieldShowtimeEve', '@CheckButtonShowtimeEve', '@CloseButtonShowtimeEve', '@EditButtonShowtimeEve', '@FieldShowtimeEveMessage', '@ValueShowtimeEve')"><i class="far fa-window-close"></i></button>
                                </td>
                            }
                        </tr>
                    }
                </table>
</code></pre>
<pre><code>

        function success(obj) {
            if (obj["Success"]) {
                document.getElementById(obj["InputField"]).style.display = "none";
                document.getElementById(obj["CheckButton"]).style.display = "none";
                document.getElementById(obj["CloseButton"]).style.display = "none";
                document.getElementById(obj["EditButton"]).style.display = "inline";
                document.getElementById(obj["MessageField"]).innerHTML = "";
                document.getElementById(obj["ValueSpan"]).style.display = "inline";
                document.getElementById(obj["ValueSpan"]).innerHTML = obj["NewValue"];
            }
            else {
                document.getElementById(obj["MessageField"]).innerHTML = obj["Message"];
            }
        }
        
        function show_edit_functions(InputField, CheckButton, CloseButton, EditButton, FieldMessage, ValueSpan) {
            document.getElementById(InputField).style.display = "block";
            document.getElementById(CheckButton).style.display = "inline";
            document.getElementById(CloseButton).style.display = "inline";
            document.getElementById(EditButton).style.display = "none";
            document.getElementById(FieldMessage).innerHTML = "";
            document.getElementById(ValueSpan).style.display = "none";
        }

        function hide_edit_functions(FieldOpeningDay, CheckButtonOpeningDay, CloseButtonOpeningDay, EditButtonOpeningDay, FieldOpeningDayMessage, ValueOpeningDay) {
            document.getElementById(FieldOpeningDay).style.display = "none";
            document.getElementById(CheckButtonOpeningDay).style.display = "none";
            document.getElementById(CloseButtonOpeningDay).style.display = "none";
            document.getElementById(EditButtonOpeningDay).style.display = "inline-block";
            document.getElementById(FieldOpeningDayMessage).innerHTML = "";
            document.getElementById(ValueOpeningDay).style.display = "inline";
        }
</code></pre>
<pre><code>

        //POST: Productions/PartialEdit
        [HttpPost]
        [ValidateAntiForgeryToken]
        [Authorize(Roles = "Admin")]
        public JsonResult PartialEdit([Bind(Include = "ProductionId,OpeningDay,ClosingDay,ShowtimeEve, ShowtimeMat")] Production production)
        {
            ModelState.Remove("Title");
            string propertyName = Request.Form["PropertyName"];
            string ProductionId = Request.Form["ProductionId"];
            int prodId = Convert.ToInt32(ProductionId);
            string OpeningDay = Request.Form["OpeningDay"];
            string ClosingDay = Request.Form["ClosingDay"];
            string ShowtimeEve = Request.Form["ShowtimeEve"];
            string ShowtimeMat = Request.Form["ShowtimeMat"];

            //Even though the title is not being modified, it needs to be set otherwise it will cause a validation error. It will not be saved to the database
            production.Title = "dummy";
            //Variable for return json
            String newValue = ""; 

            //Attach the production before setting the property to isModified. Set it after comparing to the original entry in the database or the find method will search in the local version with the same id.
            if (propertyName == "OpeningDay")
            {
                if(OpeningDay == null || OpeningDay == "")
                {
                    return Json(new { Success = false, Message = "The opening day cannot be left blank.", MessageField = Request.Form["MessageField"] });
                }
                db.Productions.Attach(production);
                db.Entry(production).Property(x => x.OpeningDay).IsModified = true;
                //Variable for return json
                newValue = production.OpeningDay.ToShortDateString();
            }
            else if (propertyName == "ClosingDay")
            {
                if (ClosingDay == null || ClosingDay == "")
                {
                    return Json(new { Success = false, Message = "The closing day cannot be left blank.", MessageField = Request.Form["MessageField"] });
                }
                db.Productions.Attach(production);
                db.Entry(production).Property(x => x.ClosingDay).IsModified = true;
                //Variable for return json
                newValue = production.ClosingDay.ToShortDateString();
            }
            else if (propertyName == "ShowtimeMat")
            {
                //Retrieve only the needed field or it will not be possible to attach production.
                DateTime? DbShowtimeEve = db.Productions.Where(u => u.ProductionId == prodId).Select(u => u.ShowtimeEve).SingleOrDefault();
                if ((ShowtimeMat == null || ShowtimeMat == "") && !DbShowtimeEve.HasValue)
                {
                    return Json(new { Success = false, Message = "Please provide a evening time before deleting the matinee time.", MessageField = Request.Form["MessageField"] });
                }
                db.Productions.Attach(production);
                db.Entry(production).Property(x => x.ShowtimeMat).IsModified = true;
                //Variable for return json
                newValue = production.ShowtimeMat.GetValueOrDefault().ToShortTimeString();
            }
            else if (propertyName == "ShowtimeEve")
            {
                //Retrieve only the needed field or it will not be possible to attach production.
                DateTime? DbShowtimeMat = db.Productions.Where(u => u.ProductionId == prodId).Select(u => u.ShowtimeMat).SingleOrDefault();
                if ((ShowtimeEve == null || ShowtimeEve == "") && !DbShowtimeMat.HasValue)
                {
                    return Json(new { Success = false, Message = "Please provide a matinee time before deleting the evening time.", MessageField = Request.Form["MessageField"] });
                }
                db.Productions.Attach(production);
                db.Entry(production).Property(x => x.ShowtimeEve).IsModified = true;
                //Variable for return json
                newValue = production.ShowtimeEve.GetValueOrDefault().ToShortTimeString();
            }
            db.Entry(production).Property(x => x.Title).IsModified = false;

            if (ModelState.IsValid)
            {
                try
                {
                    db.SaveChanges();
                    return Json(new { Success = true, NewValue = newValue, MessageField = Request.Form["MessageField"], InputField = Request.Form["InputField"], EditButton = Request.Form["EditButton"], CheckButton = Request.Form["CheckButton"], CloseButton = Request.Form["CloseButton"], ValueSpan = Request.Form["ValueSpan"] });
                }
                catch { 
                    return Json(new { Success = false, Message = "There was an error.", MessageField = Request.Form["MessageField"] });
                }
            }
            return Json(new { Success = false, Message = "There was an error", MessageField = Request.Form["MessageField"] });
        }
</code></pre>

<h3 id="7390">Direct Message Fans</h3>
<h4>Description</h4>
<p>When a current Production has at least one Cast Member that a Subscriber has favorited, we want to give the admin the ability to send all subscribers that have favorited that Cast Member a direct message.</p>
<p>Create a button before the title of each of the current Productions on the Admin Dashboard.  Make this button a font awesome icon button similar to the buttons on the Part Index page and have the icon be a message icon (similar to the one in the messaging bar).  </p>
<p>Clicking this button should pull up a modal asking the admin if they want to send all subscribers an email about this Production ("There are Subscribers that have favorited some Cast Members in this Production.  Notify Subscribers about Production?").  The messages sent should go to the Subscriber's MessageGroup that has the Admin in the UserList.  If there is no such MessageGroup, create one.</p>
<p>The message should let them know about the production.  Something like "Hey [User's name]!  One of the Cast Members that you are following is going to be playing in a new upcoming Production.  Check out [Cast Member's name] in [Production's name] opening on [Production's opening day] at Theatre Vertigo!  Get your tickets now!".  If the Subscriber has multiple favorited Cast Members in the Production, modify the message to let the Subscriber know of all of their favorited Cast Members in that Production.</p>
<h4>Implementation</h4>
<p></p>
<p></p>
<p></p>

![Direct Message Fans](GitHub-images/7390.PNG?raw=true)

<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>

            <table class="admin-tables mx-3">
            @{
                foreach (var prod in ViewData["CurrentProductionList"] as List<TheatreCMS.Models.Production>)
                {
                    <tr class="dashboard-subsection">
                        <td>
                            <p class="h5">
                                @{String hascastmemberfan = "hascastmemberfan" + ((bool)ViewData[prod.ProductionId.ToString()]).ToString(); }
                                <button class="btn comment-btn open-MessaginModal @hascastmemberfan" data-productionid="@prod.ProductionId" data-productiontitle="@prod.Title" data-hascastmemberfan="@hascastmemberfan" data-toggle="modal" data-target="#messagingModal" aria-disabled="true"><i class="fas fa-comment-dots fa-2x"></i></button>
                                @prod.Title <span class="dashboard-badges small mx-3">
                                    @Html.ActionLink("Edit", "Edit", "Productions", new { id = prod.ProductionId }, new { @class = "badge badge-pill" }) |
                                    @Html.ActionLink("Delete", "Delete", "Productions", new { id = prod.ProductionId }, new { @class = "badge badge-pill" })
                                </span>
                            </p>
                            <p class="mx-3"> @prod.OpeningDay.ToString("MM/dd/yy")-@prod.ClosingDay.ToString("MM/dd/yy")</p>
                        </td>
                    </tr>
                }
            }
        </table>
</code></pre>
<pre><code>

    //get production info to modal
    $(document).on("click", ".open-MessaginModal", function () {
        var productionId = $(this).data('productionid');
        var productionTitle = $(this).data('productiontitle');
        $(".modal-body #productionId").val(productionId);
        $(".modal-body #productionTitle").text(productionTitle);
        if ($(this).data('hascastmemberfan') === 'hascastmemberfanFalse') {
            $(".modal-body #messageModalText").text("There are no Subscribers that have favorited any Cast Members in this Production.");
            $("#sendMessagesBtn").hide();
        }
        else {
            $(".modal-body #messageModalText").text("There are Subscribers that have favorited some Cast Members in this Production.  Notify Subscribers about Production?");
            $("#sendMessagesBtn").show();
        }
    });

    //AJAX FUNCTIONS
    //the built in OnSuccess reports success if the controller was reached even if the operation in the controller results in a false.
    function AjaxSucces(obj) {
        if (obj["Success"]) {
            $(".modal-body #messageModalText").text("Messages have been sent.");
            $("#sendMessagesBtn").hide();
        }
        else {
            $(".modal-body #messageModalText").text("There was an error sending the messages. Please try again.");
        }
    }
    function AjaxFailure() {
        $(".modal-body #messageModalText").text("On Failure was called. There was an error sending the messages. Please try again.");
    }
</code></pre>
<pre><code>

    public class Message
    {
        public Message()
        {
            Sent = DateTime.Now;
            Read = false;
        }
        public int MessageID { get; set; }
        public virtual MessageGroup MessageGroup { get; set; }
        public virtual ApplicationUser Author { get; set; }
        public string Content { get; set; }
        public DateTime Sent { get; set; }
        public bool Read { get; set; }
    }
</code></pre>
<pre><code>

    public class MessageGroupUser
    {
        public int MessageGroupUserID { get; set; }
        public int MessageGroupID { get; set; }
        public String ApplicationUserID { get; set; }
        public virtual MessageGroup MessageGroup { get; set; }
        public virtual ApplicationUser ApplicationUser { get; set; }
    }
</code></pre>
<pre><code>
    
    public class MessageGroup
    {
        //using data annotations to change the display name of this property.
        [Display(Name = "Group Name")]
        //unique identifier for the group
        public string GroupName { get; set; }
        public MessageGroup()
        {
            CreatedOn = DateTime.Now;
        }
        public int MessageGroupID { get; set; }
        public virtual ApplicationUser CreatedBy { get; set; }
        public DateTime CreatedOn { get; set; }
        public virtual ICollection<Message> Messages { get; set; }
        public int UserLimit { get; set; }
        public ICollection<MessageGroupUser> MessageGroupUsers { get; set; }
    }
</code></pre>
<pre><code>

        //Send messages to all users who have favorited a castmember in the production.
        [HttpPost]
        [ValidateAntiForgeryToken]
        [Authorize(Roles = "Admin")]
        public JsonResult sendMessages()
        {
            int productionId = Convert.ToInt32(Request.Form["productionId"]);
            Production production = db.Productions.Find(productionId);
            //Generate a dictionay with Users as Keys and List of Parts/Castmembers that they favorited as Values
            Dictionary<ApplicationUser, List<Part>> usersToMessage = new Dictionary<ApplicationUser, List<Part>>();
            foreach (Part part in production.Parts)
            {
                String searchString = part.Person.CastMemberID.ToString();
                //A simple query wityh only .Contains(searchString) will return users where the searchstring is only part of the id of the castmember that was favorited.
                List<ApplicationUser> fansOfCastMember = db.Users.Where(u => u.FavoriteCastMembers == searchString || u.FavoriteCastMembers.StartsWith(searchString + ",") || u.FavoriteCastMembers.EndsWith("," + searchString) || u.FavoriteCastMembers.Contains("," + searchString + ",")).ToList();
                foreach (ApplicationUser fanOfCastMember in fansOfCastMember)
                {
                     if (!usersToMessage.ContainsKey(fanOfCastMember)) {
                        usersToMessage[fanOfCastMember] = new List<Part> { part };
                    }
                     else
                    {
                        usersToMessage[fanOfCastMember].Add(part);
                    }
                }
            }

            //Create messages to all Users in the above generated list.
            string userId = User.Identity.GetUserId();
            ApplicationUser admin = db.Users.Find(userId);
            foreach (KeyValuePair<ApplicationUser, List<Part>> entry in usersToMessage)
            {
                MessageGroup messageGroup = null;
                //Find if a messageGroup exists with ONLY the userToMessage and the admin.
                //First Find all entries in 
                foreach (MessageGroupUser messageGroupUser in entry.Key.MessageGroupUsers)
                {
                    if( messageGroupUser.MessageGroup.MessageGroupUsers.Count <=2 && messageGroupUser.MessageGroup.MessageGroupUsers.Count <= 2)
                    {
                        messageGroup = messageGroupUser.MessageGroup;
                    }
                }
              
                //If no existingMessageGroup was found with only the Admin and the userToMessage, Create new MessageGroup.
                if (messageGroup == null)
                {
                    messageGroup = new MessageGroup();
                    messageGroup.CreatedBy = admin;
                    MessageGroupUser mgu1 = new MessageGroupUser();
                    mgu1.ApplicationUser = admin;
                    mgu1.MessageGroup = messageGroup;
                    MessageGroupUser mgu2 = new MessageGroupUser();
                    mgu2.ApplicationUser = entry.Key;
                    mgu2.MessageGroup = messageGroup;
                }
                //Build Message Content
                String One_Some = "";
                String is_are = "";
                if (entry.Value.Count() == 1)
                {
                    One_Some = "One";
                    is_are = "is";
                }
                else
                {
                    One_Some = "Some";
                    is_are = "are";
                }
                string castMembers = "";
                int counter = 0;
                foreach (Part part in entry.Value)
                {
                    if (counter == 0) castMembers += part.Person.Name;
                    else if (counter == entry.Value.Count() - 1) castMembers += " and " + part.Person.Name;
                    else castMembers += ", " + part.Person.Name;
                    counter++;
                }
                String messageContent = $"Hey {entry.Key.FirstName}!  {One_Some} of the Cast Members that you are following {is_are} going to be playing in a new upcoming Production.";
                messageContent += $"  Check out {castMembers} in {production.Title} opening on {production.OpeningDay.ToShortDateString()} at Theatre Vertigo!  Get your tickets now!";
                //Create new Message
                Message message = new Message();
                message.MessageGroup = messageGroup;
                message.Author = admin;
                message.Content = messageContent;

                db.MessageGroup.Add(messageGroup);
                db.Message.Add(message);
            }
            db.SaveChanges();

            return Json(new { Success = true });
        }
</code></pre>

<h3 id="6342">Mobile Calendar</h3>
<h4>Description</h4>
<p>The calendar of events displays well in a full sized screen on a laptop, computer, or tablet. On a mobile phone, the calendar still displays correctly, but it looks very crammed.</p>
<p>We would like the events to be displayed as a list consisting events for the  current week rather than as a calendar in mobile view. You will need to find a way to obtain and display the dates of the current week. You will then need to pull data from the CalendarEvents table to display the events of the week using razor syntax.</p>
<h4>Implementation</h4>
<p></p>
<p></p>
<p></p>

![Mobile Calendar](GitHub-images/6342.PNG?raw=true)

<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>

    var events = @Html.Raw(JsonConvert.SerializeObject(ViewData["Events"]));
    var weekOffset = 0;
    $(document).ready(function () {
        GenerateCalendar(events);
        weekOffset = 0;
        GenerateWeekCalendar(events, weekOffset);
        });
    function GenerateWeekCalendar(events, offset) {
        var range = CalculateWeekRange(offset);
        setCalendarHeading(range);
        startOfWeek = range[0].setHours(0, 0, 0, 0);
        endOfWeek = range[1].setHours(0, 0, 0, 0);
        for (let event of events) {
            //THIS IF STATEMENT WORKS ONLY IF EVENTS ARE NOT MULTIPLE DAYS.
            if (GetDateTime(event.start).setHours(0, 0, 0, 0) >= startOfWeek && GetDateTime(event.start).setHours(0, 0, 0, 0) < endOfWeek) {
                CreateEvent(event);
            }
        }

        //this function calculates the begin and end date of the calendar to be displayed
        function CalculateWeekRange(offset) {
            //temporarily set start and end of week to today.
            var startOfWeek = new Date();
            var endOfWeek = new Date();
            var dayOfweek = startOfWeek.getDay();
            startOfWeek.setDate(startOfWeek.getDate() - dayOfweek + (7 * offset));
            endOfWeek.setDate(endOfWeek.getDate() - dayOfweek + 7 + (7 * offset));
            return [startOfWeek, endOfWeek];
        }

        //this function build the string for the calendar heading based on the range of dates.
        function setCalendarHeading(range) {
            if (range[0].getMonth() == range[1].getMonth()) {
                headingString = `${range[0].toLocaleString('default', { month: 'long' })} ${range[0].toLocaleString('default', { year: 'numeric' })}`;
            }
            else if (range[0].getYear() == range[1].getYear()) {
                headingString = `${range[0].toLocaleString('default', { month: 'short' })} - ${range[1].toLocaleString('default', { month: 'short' })}, ${range[0].toLocaleString('default', { year: 'numeric' })}`;
            }
            else {
                headingString = `${range[0].toLocaleString('default', { month: 'short' })} ${range[0].toLocaleString('default', { year: '2-digit' })} - ${range[1].toLocaleString('default', { month: 'short' })} ${range[1].toLocaleString('default', { year: '2-digit' })}`;
            }
            document.getElementById("MobileEventsCalendarHeading").innerHTML = headingString;
        }

        //This generates the html code for the mobile EventsCalendar
        function CreateEvent(item) {
            let text = "";
            let row = document.createElement('div');
            row.className = 'row';
            let leftCol = document.createElement('div');
            leftCol.className = 'col-dateCards';
            let card = document.createElement('div');
            card.className = 'card';
            card.style.backgroundColor = item.color || '#ffffff';
            card.style.color = BlackOrWhiteContrast(item.color || '#ffffff');
            let cardBody = document.createElement('div');
            cardBody.className = 'card-body';
            let cardTitle = document.createElement('h4');
            //cardTitle.className = 'row';
            text = document.createTextNode(GetDayOfMonth(item.start));
            cardTitle.appendChild(text);
            let cardText = document.createElement('p');
            cardText.className = 'card-text';
            text = document.createTextNode(GetDayOfWeekShort(item.start));
            cardText.appendChild(text);
            let rightCol = document.createElement('div');
            rightCol.className = 'col';
            let heading = document.createElement('h4');
            //heading.className = 'row';
            text = document.createTextNode(item.title);
            heading.appendChild(text);
            let spanDate = document.createElement('span');
            //spanDate.className = 'row';
            text = document.createTextNode(GetDate(item.start));
            spanDate.appendChild(text);
            let spanTime = document.createElement('span');
            //spanTime.className = 'row';
            text = document.createTextNode(GetTime(item.start) + " - " + GetTime(item.end));
            spanTime.appendChild(text);
            let spanTickets = document.createElement('span');
            //spanTickets.className = 'row';
            text = document.createTextNode('Tickets available: ' + item.seats);
            spanTickets.appendChild(text);
            let br1 = document.createElement('br');
            let br2 = document.createElement('br');
            let br3 = document.createElement('br');
            let br4 = document.createElement('br');

            cardBody.appendChild(cardTitle);
            cardBody.appendChild(cardText);
            card.appendChild(cardBody);
            leftCol.appendChild(card);
            row.appendChild(leftCol);
            rightCol.appendChild(heading);
            rightCol.appendChild(spanDate);
            rightCol.appendChild(br1);
            rightCol.appendChild(spanTime);
            rightCol.appendChild(br2);
            rightCol.appendChild(spanTickets);
            row.appendChild(rightCol);
            document.getElementById("mobile-calendar-container").appendChild(row)
        }
    }

    function previousWeek() {
        weekOffset -= 1;
        document.getElementById("mobile-calendar-container").innerHTML = "";
        setThisWeekBtn()
        GenerateWeekCalendar(events, weekOffset);
    }

    function nextWeek() {
        weekOffset += 1;
        document.getElementById("mobile-calendar-container").innerHTML = "";
        setThisWeekBtn()
        GenerateWeekCalendar(events, weekOffset);
    }

    function thisWeek() {
        weekOffset = 0;
        document.getElementById("mobile-calendar-container").innerHTML = "";
        setThisWeekBtn()
        GenerateWeekCalendar(events, weekOffset);
    }

    function setThisWeekBtn() {
        if (weekOffset == 0) {
            document.getElementById("thisWeek").classList.add("fc-state-disabled");
            document.getElementById("thisWeek").disabled = true;
        }
        else {
            document.getElementById("thisWeek").classList.remove("fc-state-disabled");
            document.getElementById("thisWeek").disabled = false;
        }
    }
</code></pre>
<pre><code>

    //function converts a hex color or hex shorthand color into r,g, b
    function hexToRgb(hex) {
        // Expand shorthand form (e.g. "03F") to full form (e.g. "0033FF")
        var shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i;
        hex = hex.replace(shorthandRegex, function (m, r, g, b) {
            return r + r + g + g + b + b;
        });
        
        var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
        return result ? {
            r: parseInt(result[1], 16),
            g: parseInt(result[2], 16),
            b: parseInt(result[3], 16)
        } : null;
    }

    //this function calculates the luminance of an color. It takes thre values, r g and b
    // from http://www.w3.org/TR/WCAG20/#relativeluminancedef
    function relativeLuminanceW3C(R8bit, G8bit, B8bit) {

        var RsRGB = R8bit / 255;
        var GsRGB = G8bit / 255;
        var BsRGB = B8bit / 255;

        var R = (RsRGB <= 0.03928) ? RsRGB / 12.92 : Math.pow((RsRGB + 0.055) / 1.055, 2.4);
        var G = (GsRGB <= 0.03928) ? GsRGB / 12.92 : Math.pow((GsRGB + 0.055) / 1.055, 2.4);
        var B = (BsRGB <= 0.03928) ? BsRGB / 12.92 : Math.pow((BsRGB + 0.055) / 1.055, 2.4);

        // For the sRGB colorspace, the relative luminance of a color is defined as: 
        var L = 0.2126 * R + 0.7152 * G + 0.0722 * B;

        return L;
    }

    //This function tells you to use black or white text color to contrast with the background color in hex.
    function BlackOrWhiteContrast(hex) {
        colorArray = hexToRgb(hex);
        luminance = relativeLuminanceW3C(colorArray.r, colorArray.g, colorArray.b) 
        return (luminance > 0.179) ? '#000000' : '#ffffff';
    }
</code></pre>

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
![XXX](GitHub-images/7390.PNG?raw=true)
<h5><a href="#features">Back to Features</a></h5>
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
</code></pre>
<pre><code>

        public ActionResult Subscribe()
        {
            ViewBag.SubscriptionPlans = db.SubscriptionPlan.OrderByDescending(p => p.NumberOfShows ).ToList();
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

![Add Production Photo](GitHub-images/7298.PNG?raw=true)
<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>

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

![Rotate Production Photos](GitHub-images/6612.PNG?raw=true)
<h5><a href="#features">Back to Features</a></h5>
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

<h3 id="6598">Admin Navigation Panel</h3>
<h4>Description</h4>
<p>Create the following: We want to allow the Admin the ability to easily see and go to every page on the project.  Create a button on the bottom-right of the page, above the Bug Report button.  The text for that button should read "All Pages" and should have a Font Awesome chevron icon as well.  When the button is clicked, a region should appear that contains all available pages on the site. Notice that the Delete, Details and Edit pages are not being displayed.  Exclude the pages that require an id to display.  Clicking on the text "Productions" should take the Admin to the Productions Index page.  The Admin should be able to close this menu by clicking the right chevron in the bottom-right or clicking outside of the menu.  List the pages in the Production, ProductionPhotos and Photos areas of the project.</p>
<h4>Implementation</h4>
<p>Create partial view for Admin Navigation Panel with bootstrap cards.</p>
<p>Create JQuery to hide and show Admin Navigation Panel when buttons are clicked or when the area outside the panel is clicked.</p>

![Admin Navigation Panel](GitHub-images/6598.png?raw=true)
<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>

    @if (User.Identity.IsAuthenticated && User.IsInRole("Admin"))
    {
        <script>
        /* function to make the navbar disappear when right chevron is clicked */
            function disappear_admin_navbar() {

                document.getElementById("admin_navbar_button").style.display = 'block';
                $("#admin_navbar_container").animate({ right: -350 }, "slow");
            }

            /*this function brings back the nav bar when the button is clicked.*/
            function appear_admin_navbar() {
                document.getElementById("admin_navbar_button").style.display = 'none';
                $("#admin_navbar_container").animate({right: 0}, "slow");
            }

            /* Hide the admin navbar when user clicks outside of it, only if it is not already hidden (if the button IS hidden). */
            $(document).on('click', function (e) {
                if ($(e.target).closest(".admin_navbar").length === 0) {
                    if (window.getComputedStyle(document.getElementById("admin_navbar_button")).display === "none") {
                        $("#admin_navbar_container").animate({ right: -350 }, "slow");
                        $("#admin_navbar_button").show();
                    }
                }
            });
        </script>

        <div class="admin_navbar_button admin_navbar" id="admin_navbar_button">
            <a class="" onclick="appear_admin_navbar()"><span class="vertical_text">All Pages <i class="fas fa-chevron-right"></i></span> </a>
        </div>

        <div class="admin_navbar_container admin_navbar" id="admin_navbar_container">
            <div class="admin_navbar_menu">
                <ul class="admin_navbar_main_menu card-columns">
                    <li class="card">
                        <p>@Html.ActionLink("Productions", "Index", "Productions")</p>
                        <ul class="admin_navbar_sub_menu">
                            <li>@Html.ActionLink("Create", "Create", "Productions")</li>
                            <li>@Html.ActionLink("Current", "Current", "Productions")</li>
                        </ul>
                    </li>
                    <li class="card">
                        <p>@Html.ActionLink("Production Photos", "Index", "ProductionPhotos")</p>
                        <ul class="admin_navbar_sub_menu">
                            <li>@Html.ActionLink("Create", "Create", "ProductionPhotos")</li>
                        </ul>
                    </li>
                    <li class="card">
                        <p>@Html.ActionLink("Photos", "Index", "Photo")</p>
                        <ul class="admin_navbar_sub_menu">
                            <li>@Html.ActionLink("Create", "Create", "Photo")</li>
                        </ul>
                    </li>
                </ul>
                <div class="row justify-content-end mr-1">
                    <a onclick="disappear_admin_navbar()"><i class="fas fa-chevron-right"></i></a>
                </div>
            </div>
        </div>
    }
</code></pre>

<h3 id="7858">Unique Subscription Plan</h3>
<h4>Description</h4>
<p>There is a method in the SubscriptionPlan controller that detects if a SubscriptionPlan is not unique (if one of the properties of the Plan match any of the other properties of any of the Plans in the database).  If a property is not unique, a Model error is added to the ModelState.  We also want to give the admin the ability to navigate to the SubscriptionPlan that conflicted with the Plan the we were trying to add or edit.  Add a details link at the end of each of the validation messages that appears on the page that take the User to the Details page of the SubscriptionPlan that already has that property value when the link is clicked.</p>
<h4>Implementation</h4>
<p>Update controller to include link ModelState errors. Combine error messages into ONE if pertaining to same existing plan.</p>
![XXX](GitHub-images/7390.PNG?raw=true)
<h5><a href="#features">Back to Features</a></h5>
<h4>Code</h4>
<pre><code>


        public void ComparePlans(SubscriptionPlan subscriptionPlan)
        {
            //Find similar plans in Database
            var similarPlans = db.SubscriptionPlan.AsNoTracking().Where(p =>
                p.SubscriptionLevel == subscriptionPlan.SubscriptionLevel ||
                p.PricePerYear == subscriptionPlan.PricePerYear ||
                p.NumberOfShows == subscriptionPlan.NumberOfShows).ToList();
            //if there, remove plan that is being edited from similarPlans
            similarPlans.RemoveAll(p => p.PlanId.Equals(subscriptionPlan.PlanId));
            
            string SubscriptionLevel = subscriptionPlan.SubscriptionLevel;
            decimal PricePerYear = subscriptionPlan.PricePerYear;
            int NumberOfShows = subscriptionPlan.NumberOfShows;

            foreach (var plan in similarPlans)
            {
                string ExistingSubscriptionLevel = plan.SubscriptionLevel;
                decimal ExistingPricePerYear = plan.PricePerYear;
                int ExistingNumberOfShows = plan.NumberOfShows;

                string anchor = "<a href =\"/Subscribers/SubscriptionPlans/Details/" + plan.PlanId + "\" target=\"_blank\">plan.</a>";
                string errorString = "There already is a plan";
                //comparison of users input and specified attribute, check for equal
                if (ExistingSubscriptionLevel.ToLower().Equals(SubscriptionLevel.ToLower()))
                {
                    errorString += " with the same level name";
                }
                //comparison of users input and specified attribute, check for equal
                if (ExistingPricePerYear.Equals(PricePerYear))
                {
                    errorString += ", that offers a price/year of: $" + PricePerYear;
                }
                //comparison of users input and specified attribute, check for equal
                if (ExistingNumberOfShows.Equals(NumberOfShows))
                {
                    errorString += ", that offers " + NumberOfShows + " tickets/year";
                }
                errorString += ". See " + anchor;
                ModelState.AddModelError("SubscriptionPlan", errorString);
            }
</code></pre>
