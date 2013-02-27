LiveFlexTreeview
================

Lightweight and flexible jQuery treeview that supports drag and drop of items and multiple callbacks.

The treeview has been designed for LiveFLEX cms and is used in production code.

Collapses nested ULs and adds .parent class
Allows internal markup with callbacks inside the nodes
Sortable with two ways to get the movement information
Calls back for directory click, node click, items moved and custom callbacks

Usage

The only thing that the plugin requires is a data-id attribute on each <li>. This is what we use to identify the node. In this example we use an integer that ties to a database record but you could use a path or custom data.

 

Options

handle:"selector"   The selector that is used identify the item that starts the drag.
callbackSelector:"selector"	 If you require icons or commands in your treeview, specify the selector for them here and when they are clicked on your callback will be fired.
callback:function(e){}	 The callback for your selector items. The original event is passed back so you can navigate to your own data.
itemMoved:function(csv)	 When an item is moved this function is called so you can process the movements. 'csv' is the a comma seperated value that has all movements in the format of [data-id]-[parent.data-id].[position]
dirClick:function(node)	 When a parent is clicked the <li> itself is passed back.
nodeClick:function(node)	 When a node is clicked the <li> itself is passed back.
 	  
The code below creates a treeview from the markup and ties in all its callbacks to illustrate how to use them.

var tree = $("ul.tree").liveflex_treeview({
	handle:"a.caption" //the selector that starts the drag
	,callbackSelector: "span.callback" //the selector that fires callbacks
	,callback: function(e){
		//function that's called when an item meeting the callback selector is clicked.
		//Just checking for an extra class to use different items, you could also use rel or data
		if($(e.target).hasClass("op2")){
			log($(e.target).closest("li").find(">a").text() + " > (callback 2)");
		}else{
			log($(e.target).closest("li").find(">a").text() + " > (callback 1)");
		}
	},itemMoved: function(csv){
		//the csv of the moved items [ItemId]-[NewParentId].[Position]
		$("#MoveResults").html(csv);
	},dirClick: function(node){
		//directory was clicked
		log(node.find(">a").text() + " > " + (node.hasClass("collapsed") ? " closed" : " opened"));
	},nodeClick: function(node){
		//node was clicked
		log(node.find(">a").text() + " > selected");
	}
});
$("#save").click(function(e){
	//get the moved items as id-parentId.position, used for db update
	log(tree.data("liveflex.treeview").getMoved());
	//or get the tree as a nested array, to process further
	var treeArray = tree.data("liveflex.treeview").parseTree();
});
Loading nodes dynamically

You can easily load nodes on request by responding to the dirClick event

dirClick:function(node){
	var Id = parseInt(node.attr('data-id')); //get the data id
	if(node.children('ul').length  == 0){ //if the node hasn't any children yet
		//call your server routine
		$.ajax({
			url: "getNodes.aspx?id=" + Id,
			success: function(data){
				//append the new li's
				node.append('<ul>' + data + '</ul>'); 
				//add expanded class so the folding works 
				node.addClass('expanded'); 
			} 
		}); 
	} 
} 
