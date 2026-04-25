# Signal messager playground

A simple playground for the [Signal] messaging app. Uses the unofficial [signal-cli] Docker
image by [AsamK].

## Requirements

- A persistent Docker volume (`docker volume create signalling`)
- A Signal account on your phone
- An environment file

### Account linking

1. Get out your phone, open Signal, go to Settings > Linked devices
2. Run `docker run -it --rm -v signalling:/var/lib/signal-cli ghcr.io/asamk/signal-cli:0.14.3-native link -n CLI` on the computer.
3. Click "Link new Device" in Signal
3. You can try scanning the QR code from the terminal. Great if it works. If not, copy the `sgnl` link,
runt it through a QR code generator (as text, not URL) and scan that one.
4. Approve linking

Note that it might not be smart to run the `sngl` link through an online QR code generator. I haven't
thoroughly checked reuse/replay attack possibilites. It is some kind of access token, though. Be smart!

If you have Python and the `qrcode` module installed, you can generate a code locally using this:
`python -c "import qrcode, tempfile, os; p=tempfile.mktemp('.png'); qrcode.make('SGNL_LINK_HERE').save(p); os.startfile(p)"`

### Environment file

To run the tests, you need an env file `.env` at project root:

```
ACCOUNT=+YOUR_NUMBER_HERE
RECIPIENT=+RECIPIENT_NUMBER_HERE
```

## Testing

The local tests use the [REST Client] extension for VS Code. The container exposes a JSON-RPC API
on `localhost:7583/api/v1/rpc`.

### Getting accounts

Send this payload to the RPC endpoint to list accounts:

```
{
    "jsonrpc":"2.0",
    "method":"listAccounts",
    "id": "{{$guid}}"
}
```

### Sending message

Sending messages goes like this:

```
{
  "jsonrpc":"2.0",
  "method": "send",
  "params": {
    "account": "{{$dotenv ACCOUNT}}",
    "recipient": "{{$dotenv RECIPIENT}}",
    "message": "Testing, 123"
  },
  "id": "{{$guid}}"
}
```


[Signal]: https://signal.org
[signal-cli]: https://github.com/AsamK/signal-cli
[AsamK]: https://github.com/AsamK
[REST Client]: https://marketplace.visualstudio.com/items?itemName=humao.rest-client