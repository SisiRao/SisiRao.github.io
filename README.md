{% extends "OOTD/base.html" %}

{% block content %}
{% load staticfiles %}

<div id="fh5co-main">

  <div class="container">

    <form enctype="multipart/form-data" method="post" action="{% url 'filter' %}">
      <table>
        <tr ><input style="margin-bottom: 15px" class="index-filter" type="text" name="searchword" value="" placeholder="Any keyword"></tr>       
        {% for field in form.visible_fields %}
        <td class= "index-filter-label" style="width: 10px;">{{field.label}}</td><td>{{field}}</td>
        {%endfor%}
        <td><a href="{% url 'filter' %}"><button type=submit class="btn btn-primary index-filter-btn"> See Result</button></a></td>
        {% csrf_token %}
      </table>
    </form>
    <hr>
    <div>
      <table style="margin: auto;">
        <tr>
          <td><h4 id="chat-container" colspan="8" style="font-weight: bold; color:#6d6e70;">Join Our Chat Room to Discuss One of Our Top Popular Topics Now!</h4></td>

         <td>&nbsp;&nbsp;&nbsp;</td>
          <td><a href="/chat"><button class="btn btn-primary index-filter-btn"> Join Now!</button></a></td>

        </tr>
      </table>
    </div>

    <hr>


    <div class="row" id="new-msg-container">

    </div>
    <div class="row">
      <div id="fh5co-board" data-columns>

        <!-- All outfits -->

        {% for outfit in outfits %}

        <div class="item" id="outfit-item-{{outfit.id}}">
          <div class="animate-box">
            {% load staticfiles %}
            <a id="outfit-container-{{outfit.id}}" href="{% url 'photo' outfit.id %}" class="image-popup fh5co-board-img">
              <img class="outfit-picture" id="outfit-picture-{{outfit.id}}" src="{% url 'photo' outfit.id %}" alt="Free HTML5 Bootstrap template">
            </a>
            <div class="planetmap">
              {% for cloth in clothes %}
              {% if cloth.outfit == outfit %}
              <div id="clothes-tagged-{{cloth.id}}" class="tagged" style="left:45%; top:30%;" >
                <script type="text/javascript">
                  var out_width = $("#outfit-container-"+{{outfit.id}}).width()/2;
                  var out_height = $("#outfit-container-"+{{outfit.id}}).height();
                  var inner_width = $("#outfit-picture-"+{{outfit.id}}).width();
                  var inner_height = $("#outfit-picture-"+{{outfit.id}}).height();
                  var width_ratio = inner_width/out_width;
                  var height_ratio = inner_height/out_height;
                  if (width_ratio < 1 || height_ratio < 1) {
                    $("#clothes-tagged-"+{{cloth.id}})
                    .css("top",({{cloth.top}}*100*height_ratio)+'%')
                    .css("left",({{cloth.left}}*100*width_ratio)+'%');
                  } else {
                    $("#clothes-tagged-"+{{cloth.id}})
                    .css("top",({{cloth.top}}*100)+'%')
                    .css("left",({{cloth.left}}*100)+'%');
                  }
                </script>
                <div class="tagged_circle" style="display:none;"></div>
                <a href="{% url 'getclothes' cloth.id %}">
                  <div class="tagged_title" style="top:25; left:25; display:none;" >{{cloth.description}}</div>
                </a>
              </div>
              {% endif %}
              {% endfor %}
            </div>

            <img id='outfit-id-{{outfit.id}}' style="max-width:100px; height:auto" class="tag_btn" src="{% static 'images/tag_btn.png' %}" onclick="showTags(this.id)" >
          </div>

          <div id="outfit-btn">
            <div class="col-sm-3 col-sm-offset-3">
              {% if outfit in liked_outfits %}
              <a id="outfit-like-{{outfit.id}}" onclick="like(this.id)">
                <input type="hidden" value="1" />
                <span class="glyphicon glyphicon-heart pink"/>
                <span class="likeno" style=" font-family: 'Montserrat', arial, sans-serif;"> {{outfit.likes}}</span>
              </a>

              {% else %}
              <a id="outfit-like-{{outfit.id}}" onclick="like(this.id)">
                <input type="hidden" value="0" />
                <span class="glyphicon glyphicon-heart-empty pink"/>
                <span class="likeno" style=" font-family: 'Montserrat', arial, sans-serif;"> {{outfit.likes}}</span>
              </a>
              {% endif %}
            </div>
            <div class="col-sm-3 col-sm-offset-1">
              {% if request.user == outfit.created_by %}
              <a href="{% url 'delete' outfit.id %}">
                <span class="glyphicon glyphicon-trash"></span>
              </a>
              {% endif %}
            </div>
            <div class="col-sm-2"></div>
          </div>

          <div class="fh5co-desc col-sm-12">
            <a href="{% url 'profile' outfit.created_by.id %}">{{outfit.created_by.username}}</a>
            <p> {{outfit.description}}</p>
          </div>

        </div>
        {% endfor %}

      </div>
    </div>
  </div>

  <label hidden id="user">{{user.id}}</label>
  <h3>See users worldwide</h3>
  <div id="map" style="height:400px;width:100%"></div>
  <!-- Replace the value of the key parameter with your own API key. -->
  <script async defer
		  src="https://maps.googleapis.com/maps/api/js?key=AIzaSyBSMabzaPmRAMk8G4XP0DhSlASFpgNAmDU&callback=initMap">
</script>
<script>
  var map;
  function getPosts() {
    $.ajax({
      url: "/OOTD/location",
      dataType : "json",
        //data: "latest_post="+latest_post,
        success: load_mark
      });
  }
  function initMap() {
    map = new google.maps.Map(document.getElementById('map'), {
      zoom: 2,
      center: new google.maps.LatLng(2.8,-187.3),
      mapTypeId: 'terrain'
    });
  }
// Loop through the results array and place a marker for each
// set of coordinates.
function load_mark(results) {
  for (var i = 0; i < results.features.length; i++) {
    var coords = results.features[i].geometry.coordinates;
    var latLng = new google.maps.LatLng(coords[1],coords[0]);
    var marker = new google.maps.Marker({
      position: latLng,
      map: map,
      url: '/OOTD/profile/'+results.features[i].pk
    });
    google.maps.event.addListener(marker, 'click', function() {
      window.location.href = this.url;
    });
  }
}
// Call getPosts as soon as page is finished loading
window.onload = getPosts;
// causes Posts to be re-fetched every 5 seconds
window.setInterval(getPosts, 5000);
</script>
</div>
{% endblock %}
