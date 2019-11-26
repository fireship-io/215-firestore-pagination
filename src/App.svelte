<script>
  import { FirebaseApp, User, Doc, Collection } from "sveltefire";
  import faker from "faker";
  import firebase from "firebase/app";
  import "firebase/firestore";
  import "firebase/auth";
  import "firebase/performance";
  import "firebase/analytics";

  const firebaseConfig = {
	  // Your firebase config
  };

  firebase.initializeApp(firebaseConfig);

  let pageSize = 3;
  let field = "username";

  let query = ref => ref.orderBy(field).limit(pageSize);

  function nextPage(last) {
    query = ref => ref.orderBy(field).startAfter(last[field]).limit(pageSize);
  }

  function prevPage(first) {
    query = ref => ref.orderBy(field).endBefore(first[field]).limitToLast(pageSize);
  }

  
</script>

<FirebaseApp {firebase}>

  <div class="list">
    <Collection path={'customers'} {query} let:data let:first let:last>

      {#each data as cust}
        <section>
          <h3>{cust.username}</h3>
          <p>{cust.id}</p>
          <img src={cust.avatar} width="100px" alt="avatar" />
        </section>
      {/each}

      <span class="spinner" slot="loading">Loading comments...</span>


	<div slot="after">
        <button on:click={() => prevPage(first)}>👈 go back</button>
        <button on:click={() => nextPage(last)}>forge ahead 👉</button>
    </div>

    </Collection>

  </div>

</FirebaseApp>





<style>
  .list {
	min-height: 80vh;
    width: 75vw;
    margin: 10px auto;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: space-around;
  }

  @keyframes spinner {
    to {
      transform: rotate(360deg);
    }
  }

  .spinner:before {
    content: "";
    box-sizing: border-box;
    position: absolute;
    top: 50%;
    left: 50%;
    width: 20px;
    height: 20px;
    margin-top: -10px;
    margin-left: -10px;
    border-radius: 50%;
    border: 2px solid #ccc;
    border-top-color: #000;
    animation: spinner 0.6s linear infinite;
  }

  section {
    text-align: center;
    border-bottom: 1px solid gray;
    margin-bottom: 10px;
  }
  img {
    padding-bottom: 10px;
  }
  button {
	  width: 300px;
	  font-size: 1.5em;
  }
</style>