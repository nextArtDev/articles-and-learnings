# How to automatically sign in after sign up

## Backend

```typescript
// pages/api/auth/signup.js
import { NextApiRequest, NextApiResponse } from 'next';
import { createUser } from '../../../lib/user'; // Assume you have a createUser function
import { signIn } from 'next-auth/react';

export default async function signup(req: NextApiRequest, res: NextApiResponse) {
  const { email, password, name } = req.body;

  try {
    // Add your logic to create a user in your database
    const user = await createUser({ email, password, name });

    // If the user is created successfully, sign them in
    const result = await signIn('credentials', {
      redirect: false, // Do not redirect after signin
      email,
      password,
    });

    if (result.error) {
      // Handle errors if sign in fails
      res.status(401).json({ message: result.error });
      return;
    }

    // If sign in is successful, return a success response
    res.status(200).json({ message: 'Signed up and signed in successfully!' });
  } catch (error) {
    // Handle errors in user creation
    res.status(500).json({ message: 'Error signing up user', error: error.message });
  }
}
```

## frontend 

```typescript
// pages/signup.js (or wherever your sign-up form lives)
import { useState } from 'react';
import { signIn } from 'next-auth/react';

const SignupPage = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [name, setName] = useState('');

  const handleSubmit = async (event) => {
    event.preventDefault();

    // Send the user data to your API route for registration
    const res = await fetch('/api/auth/signup', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ email, password, name }),
    });

    const data = await res.json();

    if (res.ok) {
      // Registration successful, now sign in the user
      signIn('credentials', {
        email,
        password,
        callbackUrl: '/' // Redirect to homepage after sign in
      });
    } else {
      // Handle registration errors
      console.error(data.message);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={name} onChange={(e) => setName(e.target.value)} placeholder="Name" />
      <input type="email" value={email} onChange={(e) => setEmail(e.target.value)} placeholder="Email" />
      <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="Password" />
      <button type="submit">Sign Up</button>
    </form>
  );
};

export default SignupPage;
```