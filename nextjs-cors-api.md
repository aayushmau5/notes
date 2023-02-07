I want to set 'Access-Control-Allow-Origin' header in NextJS API routes.

How to do this?

Do I get res.setHeaders kinda helper? Yes. It's res.setHeader(key, value).

One example(Need to verify whether it works or not): https://github.com/vercel/next.js/tree/canary/examples/api-routes-cors

I just had to set header.

Example:

```
export default function handler(req, res) {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.json({});
}
```
