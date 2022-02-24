<script>
    import {
		Heading,
		Container,
        Form,
        Radio,
        Stack,
		Text,
		NumberInput,
		TextInput,
		Button
    } from "@kahi-ui/framework";
	var listType = "";
	var showCustom = false;
	var minVal;
	var maxVal;
	var ignoreNumsText;
	var lot = [];
	function resetLot() {
		lot = [];
		if (listType === 'custom') showCustom = true;
		else if (listType === '9ping') {
			showCustom = false;
			for (var i = 1; i < 35; i++) {
				lot.push(i);
			}
			lot.splice(lot.indexOf(8), 1);
			lot.splice(lot.indexOf(33), 1);
		} else if (listType === '9ho') {
			showCustom = false;
			for (var i = 1; i < 35; i++) {
				lot.push(i);
			}
			lot.splice(lot.indexOf(5), 1);
			lot.splice(lot.indexOf(16), 1);
		}
	}
	function createLot() {
		lot = []
		for (var i = minVal; i < maxVal+1; i++) {
			lot.push(i);
		}
	}
	var genType = 1;
	var perRow = 10;
	var groupNum = 2;
</script>

<Container>
	<br>
	<Heading>Random Number Generator</Heading>
	<br>
	<Text is="strong">
		Select a default list
	</Text>
	
	<Stack spacing="small" margin_top="small">
		<Form.Group
			logic_name="radio-listType"
			bind:logic_state={listType}
			on:change={resetLot}
		>
			<Radio
				id="radio-listType-9ping"
				palette="accent"
				sizing="tiny"
				value="9ping"
			>
				9 Ping
			</Radio>
	
			<Radio
				id="radio-listType-9ho"
				palette="accent"
				sizing="tiny"
				value="9ho"
			>
				9 Ho
			</Radio>
	
			<Radio
				id="radio-listType-custom"
				palette="accent"
				sizing="tiny"
				value="custom"
			>
				Custom List
			</Radio>
		</Form.Group>
	</Stack>
	{#if showCustom}
	<br>
	<Text is="strong">
		Enter the starting number (inclusive)
	</Text>
	<NumberInput bind:value={minVal} on:change={createLot} spacing="small" margin_top="small" />
	<Text is="strong">
		Enter the final number (inclusive)
	</Text>
	<NumberInput bind:value={maxVal} on:change={createLot} spacing="small" margin_top="small" />
	<Text is="strong">
		Enter the numbers to ignore (separated by commas)
	</Text>
	<TextInput bind:value={ignoreNumsText} on:change={() => {
		var ignoreNums = ignoreNumsText.split(',');
		for (var i = 0; i < ignoreNums.length; i++) { 
			ignoreNums[i] = parseInt(ignoreNums[i]);
			lot.splice(lot.indexOf(ignoreNums[i]), 1);
		}
	}} spacing="small" margin_top="small" />
	<br>
	{/if}
	<br>
	<Text is="strong" spacing="large" margin_top="large">
		Generation Type
	</Text>
	<Stack spacing="small" margin_top="small">
		<Form.Group
			logic_name="radio-genType"
			bind:logic_state={genType}
		>
			<Radio
				id="radio-genType-1"
				palette="accent"
				sizing="tiny"
				value=1
			>
				Single Person
			</Radio>
	
			<Radio
				id="radio-genType-2"
				palette="accent"
				sizing="tiny"
				value=2
			>
				Whole List
			</Radio>

			<Radio
			id="radio-genType-3"
			palette="accent"
			sizing="tiny"
			value=3
		>
			Divide into Groups
		</Radio>
		</Form.Group>
	</Stack>
	<br>
	{#if genType == 2}
	<Text is="strong">
		Enter the number of people per row
	</Text>
	<NumberInput bind:value={perRow} spacing="small" margin_top="small" />
	<br><br>
	{/if}
	{#if genType == 3}
	<Text is="strong">
		Enter the number of groups
	</Text>
	<NumberInput bind:value={groupNum} spacing="small" margin_top="small" />
	<br><br>
	{/if}
	<Button palette="affirmative" on:click={() => {
		function addToTwo(str) {
			if (str.length == 1) return '0' + str; 
			else return str;
		}
		var generated = lot;
		for (var i = 0; i < generated.length; i++) {
			var j = Math.floor(Math.random() * generated.length);
			var temp = generated[i];
			generated[i] = generated[j];
			generated[j] = temp;
		}
		if (genType == 1) {
			var num = generated[Math.floor(Math.random() * generated.length)];
			document.getElementById('result').textContent = num;
		} else if (genType == 2) {
			function addToStr(str, arr, max, currInd) {
				if (arr.length === 0) return str;
				str += '\n [' + addToTwo((currInd + 1).toString()) + '] '
				for (var i = 0; i < max; i++) {
					if (arr[i] === undefined) break;
					str += addToTwo(arr[i].toString()) + ' ';
				}
				arr.splice(0, max);
				return addToStr(str, arr, max, currInd + max);
			}
			document.getElementById('result').textContent = addToStr('', generated, perRow, 0) + '\n\n';
		} else if (genType == 3) {
			function addToStr(str, arr, currGroupInd) {
				if (arr.length === 0) return str;
				str += '\n [' + addToTwo((currGroupInd + 1).toString()) + '] '
				var pplNum = Math.ceil(arr.length / (groupNum - currGroupInd));
				for (var i = 0; i < pplNum; i++) {
					if (arr[i] === undefined) break;
					str += addToTwo(arr[i].toString()) + ' ';
				}
				arr.splice(0, pplNum);
				return addToStr(str, arr, currGroupInd + 1);
			}
			document.getElementById('result').textContent = addToStr('', generated, 0) + '\n\n';
		}
		resetLot();
		if (listType === 'custom') createLot;
	}}>Go!</Button>
	<br><br>
	<Text id="result" is="pre" style="font-weight: bold; font-size: x-large; font-family:'Courier New', Courier, monospace"></Text>
	<br><br>
</Container>