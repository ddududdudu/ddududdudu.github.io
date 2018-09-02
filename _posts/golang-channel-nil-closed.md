nil 채널
  receive block
  send block
  close panic

closed채널
  receive zero value
  send panic
  close panic
