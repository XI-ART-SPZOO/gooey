# Gooey
Gooey is a GUI system for the [Defold](https://www.defold.com) game engine. It is inspired by the excellent [Dirty Larry](https://github.com/andsve/dirtylarry) library.

# Installation
You can use Gooey in your own project by adding this project as a [Defold library dependency](http://www.defold.com/manuals/libraries/). Open your game.project file and in the dependencies field under project add:

https://github.com/britzl/gooey/archive/master.zip

# Usage
The Gooey system is encapsulated in a single Lua module without any visual components. It makes very little assumptions of the look and feel of the UI components it supports. Instead Gooey focuses on providing stable input and state handling and lets the user decide which states matter and how they should be presented visually. Gooey supports the following component types:

* Button
* Checkbox
* Radio button
* List
* Input text

## gooey.button(node_id, action_id, action, fn)
Perform input and state handling for a button

**PARAMETERS**
* ```node_id``` (string|hash) - Id of the node representing the clickable area, typically the background of the button
* ```action_id``` (hash) - Action id as received from on_input()
* ```action``` (table) - Action as received from on_input()
* ```fn``` (function) - Function to call when the button is clicked/tapped on. A button is considered clicked/tapped if both a pressed and released action has been detected inside the bounds of the node. The function will get the same state table as described below passed as its first argument

**RETURN**
* ```button``` (table) - State data for the button based on current and previous input actions

The state table contains the following fields:

* ```node``` (node) - The node itself
* ```enabled``` (boolean) - true if the node is enabled
* ```over``` (boolean) - true if user action is inside the node
* ```over_now``` (boolean) - true if user action moved inside the node this call
* ```out_now``` (boolean) - true if user action moved outside the node this call
* ```pressed``` (boolean) - true if the button is pressed
* ```pressed_now``` (boolean) - true if the button was pressed this call
* ```released_now``` (boolean) - true if the button was released this call

**EXAMPLE**

	local gooey = require "gooey.gooey"

	local function update_button(button)
		if button.pressed_now then
			gui.play_flipbook(button.node, hash("button_pressed"))
		elseif button.released_now then
			gui.play_flipbook(button.node, hash("button_normal"))
		elseif not button.pressed and button.over_now then
			gui.play_flipbook(button.node, hash("button_over"))
		elseif not button.pressed and button.out_now then
			gui.play_flipbook(button.node, hash("button_normal"))
		end
	end

	function on_input(self, action_id, action)
		update_button(gooey.button("button/bg", action_id, action, function(button)
			print("pressed")
		end))
	end

## gooey.checkbox(node_id, action_id, action, fn)
Perform input and state handling for a checkbox

**PARAMETERS**
* ```node_id``` (string|hash) - Id of the node representing the clickable area
* ```action_id``` (hash) - Action id as received from on_input()
* ```action``` (table) - Action as received from on_input()
* ```fn``` (function) - Function to call when the checkbox is checked/unchecked on. A checkbox is considered checked/unchecked if both a pressed and released action has been detected inside the bounds of the node. The function will get the same state table as described below passed as its first argument

**RETURN**
* ```checkbox``` (table) - State data for the checkbox based on current and previous input actions

The state table contains the following fields:

* ```node``` (node) - The node itself
* ```enabled``` (boolean) - true if the node is enabled
* ```over``` (boolean) - true if user action is inside the node
* ```over_now``` (boolean) - true if user action moved inside the node this call
* ```out_now``` (boolean) - true if user action moved outside the node this call
* ```checked``` (boolean) - The checkbox state (checked/unchecked)
* ```pressed``` (boolean) - true if the checkbox is pressed (ie mouse/touch down but not yet released)
* ```pressed_now``` (boolean) - true if the checkbox was pressed this call
* ```released_now``` (boolean) - true if the checkbox was released this call

**EXAMPLE**

	local gooey = require "gooey.gooey"

	local function update_checkbox(checkbox)
		if checkbox.released_now then
			if checkbox.checked then
				gui.play_flipbook(checkbox.node, hash("checkbox_checked"))
			else
				gui.play_flipbook(checkbox.node, hash("checkbox_unchecked"))
			end
		elseif not checkbox.pressed and checkbox.over_now then
			gui.play_flipbook(checkbox.node, hash("checkbox_over"))
		elseif not checkbox.pressed and checkbox.out_now then
			gui.play_flipbook(checkbox.node, hash("checkbox_normal"))
		end
	end

	function on_input(self, action_id, action)
		update_checkbox(gooey.checkbox("checkbox/bg", action_id, action, function(checkbox)
			print("checked", checkbox.checked)
		end))
	end

## gooey.radio(node_id, group, action_id, action, fn)
Perform input and state handling for a radio button

**PARAMETERS**
* ```node_id``` (string|hash) - Id of the node representing the clickable area
* ```action_id``` (hash) - Action id as received from on_input()
* ```action``` (table) - Action as received from on_input()
* ```fn``` (function) - Function to call when the radio button is selected. A radio button is considered selected if both a pressed and released action has been detected inside the bounds of the node. The function will get the same state table as described below passed as its first argument

**RETURN**
* ```radio``` (table) - State data for the radio button based on current and previous input actions

The state table contains the following fields:

* ```node``` (node) - The node itself
* ```enabled``` (boolean) - true if the node is enabled
* ```over``` (boolean) - true if user action is inside the node
* ```over_now``` (boolean) - true if user action moved inside the node this call
* ```out_now``` (boolean) - true if user action moved outside the node this call
* ```selected``` (boolean) - The radio button state
* ```pressed``` (boolean) - true if the radio button is pressed (ie mouse/touch down but not yet released)
* ```pressed_now``` (boolean) - true if the radio button was pressed this call
* ```released_now``` (boolean) - true if the radio button was released this call

**EXAMPLE**

	local gooey = require "gooey.gooey"

	local function update_radio(radio)
		if radio.released_now then
			if radio.selected then
				gui.play_flipbook(radio.node, hash("radio_selected"))
			else
				gui.play_flipbook(radio.node, hash("radio_normal"))
			end
		elseif not radio.pressed and radio.over_now then
			gui.play_flipbook(radio.node, hash("radio_over"))
		elseif not radio.pressed and radio.out_now then
			gui.play_flipbook(radio.node, hash("radio_normal"))
		end
	end

	function on_input(self, action_id, action)
		update_radio(gooey.radio("radio1/bg", "MYGROUP", action_id, action, function(radio)
			print("selected 1", radio.selected)
		end))
		update_radio(gooey.radio("radio2/bg", "MYGROUP", action_id, action, function(radio)
			print("selected 2", radio.selected)
		end))
	end

## gooey.list(node_id, group, action_id, action, fn)
Perform input and state handling for a list of items

**PARAMETERS**
* ```root_id``` (string|hash) - Id of the root node to which the list items are children. **IMPORTANT** This node should be as high as the visible part of the list
* ```item_ids``` (table) - Table with a list of list item ids (hash|string)
* ```action_id``` (hash) - Action id as received from on_input()
* ```action``` (table) - Action as received from on_input()
* ```fn``` (function) - Function to call when a list item is selected. A list item is considered selected if both a pressed and released action has been detected inside the bounds of the item. The function will get the same state table as described below passed as its first argument

**RETURN**
* ```list``` (table) - State data for the list based on current and previous input actions

The state table contains the following fields:

* ```root``` (node) - The root node
* ```enabled``` (boolean) - true if the node is enabled
* ```items``` (table) - The list items as nodes
* ```over``` (boolean) - true if user action is over any list item
* ```over_item``` (number) - Index of the list item the user action is over
* ```over_item_now``` (number) - Index of the list item the user action moved inside this call
* ```out_item_now``` (number) - Index of the list item the user action moved outside this call

* ```selected_item``` (number) - Index of the selected list item
* ```pressed_item``` (number) - Index of the pressed list item (ie mouse/touch down but not yet released)
* ```pressed_item_now``` (number) - Index of the list item the user action pressed this call
* ```released_item_now``` (number) - Index of the list item the user action released this call

**EXAMPLE**

	local gooey = require "gooey.gooey"

	local function update_list(list)
		for i,item in ipairs(list.items) do
			if i == list.pressed_item then
				gui.play_flipbook(item, hash("item_pressed"))
			elseif i == list.selected_item then
				gui.play_flipbook(item, hash("item_selected"))
			else
				gui.play_flipbook(item, hash("item_normal"))
			end
		end
	end

	function on_input(self, action_id, action)
		update_list(gooey.list("list/root", { "item1/bg", "item2/bg", "item3/bg", "item4/bg", "item5/bg" }, action_id, action, function(list)
			print("selected", list.selected_index)
		end))
	end

## gooey.input(node_id, group, action_id, action, fn)
Perform input and state handling for a text input field

**PARAMETERS**
* ```node_id``` (string|hash) - Id of the node representing the clickable area
* ```action_id``` (hash) - Action id as received from on_input()
* ```action``` (table) - Action as received from on_input()
* ```fn``` (function) - Function to call when the radio button is selected. A radio button is considered selected if both a pressed and released action has been detected inside the bounds of the node. The function will get the same state table as described below passed as its first argument

**RETURN**
* ```radio``` (table) - State data for the radio button based on current and previous input actions

The state table contains the following fields:

* ```node``` (node) - The node itself
* ```enabled``` (boolean) - true if the node is enabled
* ```over``` (boolean) - true if user action is inside the node
* ```over_now``` (boolean) - true if user action moved inside the node this call
* ```out_now``` (boolean) - true if user action moved outside the node this call
* ```selected``` (boolean) - The radio button state
* ```pressed``` (boolean) - true if the radio button is pressed (ie mouse/touch down but not yet released)
* ```pressed_now``` (boolean) - true if the radio button was pressed this call
* ```released_now``` (boolean) - true if the radio button was released this call

**EXAMPLE**

	local gooey = require "gooey.gooey"

	local function update_radio(radio)
		if radio.released_now then
			if radio.selected then
				gui.play_flipbook(radio.node, hash("radio_selected"))
			else
				gui.play_flipbook(radio.node, hash("radio_normal"))
			end
		elseif not radio.pressed and radio.over_now then
			gui.play_flipbook(radio.node, hash("radio_over"))
		elseif not radio.pressed and radio.out_now then
			gui.play_flipbook(radio.node, hash("radio_normal"))
		end
	end

	function on_input(self, action_id, action)
		update_radio(gooey.radio("radio1/bg", "MYGROUP", action_id, action, function(radio)
			print("selected 1", radio.selected)
		end))
		update_radio(gooey.radio("radio2/bg", "MYGROUP", action_id, action, function(radio)
			print("selected 2", radio.selected)
		end))
	end
