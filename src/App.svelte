<script>
  import { flip } from 'svelte/animate';

  let applicants = [
    {
      "Heading": "Players",
      "names": ["DHONI", "KOHLI", "ROHIT", "JADEJA", "PANDHYA"]
    },
    {
      "Heading": "Sold",
      "names": ["CONWAY", "DUPLESIS"]
    },
    {
      "Heading": "Unsold",
      "names": ["WARNER"]
    }
  ];

  let hoveringOverApplicant;

  function dragStart(event, applicantIndex, nameIndex) {
    const data = { applicantIndex, nameIndex };
    event.dataTransfer.setData('text/plain', JSON.stringify(data));
  }

  function drop(event, applicantIndex) {
    event.preventDefault();
    const json = event.dataTransfer.getData("text/plain");
    const data = JSON.parse(json);

    const [name] = applicants[data.applicantIndex].names.splice(data.nameIndex, 1);

    applicants[applicantIndex].names.push(name);
    applicants = [...applicants]; // Make a copy to trigger reactivity

    hoveringOverApplicant = null;
  }

  function editName(applicantIndex, nameIndex) {
    const newName = prompt("Enter a new name:");
    if (newName) {
      applicants[applicantIndex].names[nameIndex] = newName;
      applicants = [...applicants]; // Make a copy to trigger reactivity
    }
  }

  function deleteName(applicantIndex, nameIndex) {
    applicants[applicantIndex].names.splice(nameIndex, 1);
    applicants = [...applicants]; // Make a copy to trigger reactivity
  }
</script>

<h1 style="color:Maroon;">IPL Auction</h1>

<div class="applicants-container">
  {#each applicants as applicant, applicantIndex (applicant)}
    <div class="applicant" animate:flip>
      <b>{applicant.Heading}</b>
      <ul
        class:hovering={hoveringOverApplicant === applicant.Heading}
        on:dragenter={() => hoveringOverApplicant = applicant.Heading}
        on:dragleave={() => hoveringOverApplicant = null}
        on:drop={event => drop(event, applicantIndex)}
        ondragover="return false"
      >
        {#each applicant.names as name, nameIndex (name)}
          <li
            draggable={true}
            on:dragstart={event => dragStart(event, applicantIndex, nameIndex)}
          >
            {name}
            <button on:click={() => editName(applicantIndex, nameIndex)}>Edit</button>
            <button on:click={() => deleteName(applicantIndex, nameIndex)}>Delete</button>
          </li>
        {/each}
      </ul>
    </div>
  {/each}
</div>

<style>
	.applicants-container {
	  display: grid;
	  grid-template-columns: repeat(3, 1fr);
	  grid-gap: 20px;
	}
  
	.applicant {
	  background-color: whitesmoke;
	  padding: 10px;
	  border-radius: 5px;
	}
  
	.applicant ul {
	  list-style: none;
	  margin: 0;
	  padding: 0;
	}
  
	.applicant li {
	  margin-bottom: 10px;
	}
  
	.applicant li:hover {
	  cursor: move;
	  background-color: yellowgreen;
	}
  
	.applicant li button {
	  margin-left: 10px;
	}
  
	.applicant li button:hover {
	  background-color: brown;
	}
  </style>