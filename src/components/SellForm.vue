<template>
  <img
    src="https://media.karousell.com/media/photos/products/2022/9/19/apple_iphone_12_64gb_1663598912_35031561_progressive.jpg"
    alt="Left Image"
    class="left-image"
  />

  <form @submit.prevent="submitForm">
    <label for="iphoneName">iPhone Name:</label>
    <input v-model="iphoneName" type="text" id="iphoneName" required />

    <label for="rating">Rating:</label>
    <select v-model="rating" id="rating" required>
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
      <option value="4">4</option>
      <option value="5">5</option>
    </select>

    <label for="description">Description:</label>
    <textarea v-model="description" id="description" required></textarea>

    <label for="picture">Picture URL:</label>
    <input v-model="pictureURL" type="text" id="picture" required />

    <label for="price">Price:</label>
    <input v-model.number="price" type="number" id="price" required />

    <button type="submit">Submit</button>
  </form>
  <div>
    <CustomPopup v-if="showPopup" @closePopup="showPopup = false" />
  </div>
</template>

<script>
import { ref } from 'vue';
import { collection, addDoc } from 'firebase/firestore';
import { db } from '../../firebase.js';
import confetti from 'canvas-confetti'; // Import the canvas-confetti library

export default {
  setup() {
    const iphoneName = ref('');
    const rating = ref(5); // Default to highest rating
    const description = ref('');
    const pictureURL = ref('');
    const price = ref(0);
    const showPopup = ref(false);

    const showConfetti = () => {
      const duration = 10 * 1000; // Duration in milliseconds (10 seconds)

      // Configure confetti settings
      confetti({
        particleCount: 200,
        spread: 200,
        origin: { y: 0.6 },
      });

      // Clear confetti after the specified duration
      setTimeout(() => confetti.reset(), duration);
    };

    const submitForm = async () => {
      try {
        // Reference to the Firebase 'iphones' collection
        const iphoneCollection = collection(db, 'inventory');
        // Create and add a new document with the form data
        const docRef = await addDoc(iphoneCollection, {
          name: iphoneName.value,
          rating: parseInt(rating.value, 10), // Ensure that rating is stored as a number
          description: description.value,
          picture: pictureURL.value,
          price: price.value,
        });
        console.log(`iPhone added with ID: ${docRef.id}`);

        // Reset form fields after submission
        iphoneName.value = '';
        rating.value = '5';
        description.value = '';
        pictureURL.value = '';
        price.value = 0;

        // Show the pop-up
        showPopup.value = true;
        // Show confetti
        
        showConfetti();
        window.alert('Your form has been submitted!');
      } catch (error) {
        console.error('Error adding iPhone:', error);
      }
    };

    return { iphoneName, rating, description, pictureURL, price, submitForm, showPopup };
  },
};
</script>

<style scoped>
form {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

label {
  display: block;
  margin-top: 20px;
  font-size: 14px;
  font-weight: 600;
  color: #333;
}

input[type="text"],
input[type="number"],
textarea,
select {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  font-family: 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
}

textarea {
  resize: vertical;
}

button {
  background-color: #007aff;
  color: white;
  border: none;
  padding: 10px 15px;
  margin-top: 10px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #005ecb;
}

/* This pseudo-element clears floats */
form::after {
  content: "";
  display: table;
  clear: both;
}

/* Responsive adjustments */
@media (max-width: 600px) {
  form {
    width: 90%;
    padding: 15px;
  }

  label {
    margin-top: 15px;
    font-size: 12px;
  }

  button {
    padding: 10px;
    font-size: 14px;
  }
}

.container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  max-width: 1200px;
  margin: 50px auto;
}

.left-image,
.right-image {
  max-width: 200px; /* Adjust the size as needed */
  height: auto;
}

form {
  flex-grow: 1;
  max-width: 600px;
  padding: 20px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
</style>