<script lang="ts">
	import { onMount } from 'svelte'

	const REQ = "願書取得時"
	const APP = "出願時"
	const RES = "合格発表後"
	const K2 = "角型2号"
	const N2 = "長型2号"
	const N3 = "長型3号"
	const N4 = "長型4号"
	const FORM = "願書同封"
	const incl = (a) => a + "が入る封筒"

	// { name: "", when: , price: "", envelope: , required: true },


	let colleges = {
		"東京": [
			{ name: "(10)受験票が入る返信用封筒", when: APP, price: 364, envelope: N3, required: true },
			{ name: "(12)1次の得点開示が入る返信用封筒", when: APP, price: 84, envelope: N3, required: false },
			{ name: "(11)2次合否が入る返信用封筒", when: APP, price: 0, envelope: N3, required: true, note: "切手不要" },
			{ name: "願書を送るための封筒", when: APP, price: -1, envelope: K2, required: true, note: "書留" },
		],
		"大阪・工": [
			{ name: "願書請求のための返信用封筒A", when: REQ, price: 140, envelope: K2, required: true },
			{ name: "Aを送るための封筒", when: REQ, price: 84, envelope: incl(K2), required: true },
			{ name: "(4)受験票が入る返信用封筒", when: APP, price: 414, envelope: FORM, required: true },
			{ name: "願書を送るための封筒", when: APP, price: -1, envelope: K2, required: true, note: "書留" },
			{ name: "得点開示が入る返信用封筒B", when: RES, price: 404, envelope: N3, required: false },
			{ name: "Bを送るための封筒", when: RES, price: 84, envelope: incl(N3), required: false },


		],
		"大阪・基礎工": [
			{ name: "願書請求のための返信用封筒A", when: REQ, price: 140, envelope: K2, required: true, note: "1部の場合" },
			{ name: "Aを送るための封筒", when: REQ, price: 84, envelope: incl(K2), required: true },
			{ name: "(6)受験票が入る返信用封筒", when: APP, price: 384, envelope: FORM, required: true },
			{ name: "(7)合格通知が入る返信用封筒", when: APP, price: 404, envelope: FORM, required: true },
			// { name: "願書を送るための", when: REQ, price: 84, envelope: INCL_K2, required: true },
			
		],
		"筑波・情": [
			{ name: "(10)受験票が入る返信用封筒", when: APP, price: 374, envelope: N3, required: true },
			{ name: "(11)願書を送るための封筒", when: APP, price: -1, envelope: K2, required: true }
			
		],
	}

	let selection = []
	let done = {}
	let priceSum = 0
	$: {
		priceSum = selection.reduce((sum, cur, ci, _) => 
			sum + colleges[cur].reduce((p, c, i, a) => c.price == -1 ? p : p + c.price, 0)
			, 0)
	}

	onMount(() => {
		for (const name of Object.keys(colleges)) {
			done[name] = {}
		}
	})

	// let note = []
	// function calcCombination(price: number) {

	// }

</script>

<main>
	<div id="school">
		{#each Object.keys(colleges) as name}
			<label><input type="checkbox" bind:group={selection} value={name}>{name}</label>
		{/each}
	</div>

	<div id="result">
		<table>
			<tbody>
				<tr>
					<th></th>
					<th>どこに</th>
					<th>いつ</th>
					<th>なにを出す</th>
					<th>提出必須？</th>
					<th>切手</th>
					<th>封筒</th>
					<th>備考</th>
				</tr>
				{#each selection as name}
					{#each colleges[name] as it}
						<tr>
							<td><input type="checkbox" bind:value={done[name][it.name]}></td>
							<td>{name}</td>
							<td>{it.when}</td>
							<td style="text-align: left;">{it.name}</td>
							<td>{it.required ? "YES" : "NO"}</td>
							<td style="text-align: right;">{it.price == -1 ? "書留" : ""+ it.price + "円"}</td>
							<td>{it.envelope}</td>
							<td>{it.note ? it.note : ""}</td>
						</tr>
					{/each}
				{/each}
				<tr class="sum">
					<td></td>
					<td></td>
					<td></td>
					<td></td>
					<td></td>
					<td style="text-align: right;">{priceSum}円</td>
					<td></td>
					<td></td>
				</tr>
			</tbody>
		</table>
	</div>

	<ul>
		<li><strong>必ず募集要項を自身で確認してください</strong></li>
		<li>カッコの番号は募集要項に対応します</li>
		<li>金額さえあれば切手の組み合わせは自由</li>
		<li>切手はコンビニ・郵便局で買える</li>
		<li>封筒は郵便局で買うと高いらしい</li>
	</ul>
</main>

<style>
	main {
		padding: 1em;
		margin: 0 auto;
	}

	th {
		padding:0 1rem;
	}
	
	td { 
		text-align: center;
	}

	.sum {
		font-size: 1.2rem;
	}

	#school {
		margin-bottom: 2rem;
		border: 1px solid #ddd;
		border-radius: 3px;
		padding: 1rem;
	}

	#school > label {
		display: inline;
		margin-right: 1rem;
	}
</style>