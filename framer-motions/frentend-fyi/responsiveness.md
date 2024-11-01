# Responsiveness

## Responsiveness without media queries

examples:

```typescript

// 1
.container {
  display: grid;
  grid-template-columns:
    repeat(auto-fit, minmax(100px, 1fr));
}

// 2
.container {
  display: flex;
  flex-wrap: wrap;
}

.child {
  width: 100px;
  flex-grow: 1;
}

//clamp() is a new math function that allows you to set a value based on a minimum, maximum and preferred value. 
.image {
  width: clamp(50%, 480px, 100%);
}


//min() and max() are two other math functions that can be very useful in responsive design. They allow you to set a value based on the minimum or maximum of two values.

.video {
  width: min(50%, 480px);
}

.video {
  width: max(50%, 480px);
}

```