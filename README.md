# KIRUI-ELKANA
Creating a Counter Web App
                           README.md 
🧮 Counter Web App (Gleam)
A simple Counter Web Application built using Gleam on the BEAM runtime.
This app demonstrates how to create a web server and manage state safely using processes.

🚀 What This App Does
•	Starts a web server on port 3000
•	Stores a counter in memory
•	Shows the current counter value
•	Increments the counter via an HTTP request

🛠 Requirements
Make sure you have installed:
•	Gleam
•	Erlang / OTP
Check versions:
gleam --version
erl -version

📦 Create the Project
gleam new counter_app
cd counter_app

📄 Add Dependencies
Edit gleam.toml and replace the dependencies section with:
[dependencies]
gleam_stdlib = "~> 0.34"
gleam_http = "~> 3.6"
gleam_erlang = "~> 0.25"
Download dependencies:
gleam deps download

📁 Project Structure
counter_app/
├── src/
│   ├── counter.gleam
│   └── counter_app.gleam
├── gleam.toml
└── README.md

🧠 Create the Counter Process
Create a file called src/counter.gleam and add:
import gleam/erlang/process

pub type Msg {
  Get(process.Pid(Int))
  Inc
}

pub fn start() -> process.Pid(Msg) {
  process.start(loop(0))
}

fn loop(count: Int) {
  process.receive(fn
    Get(reply_to) -> {
      process.send(reply_to, count)
      loop(count)
    }

    Inc -> loop(count + 1)
  )
}

🌐 Create the Web Server
Open src/counter_app.gleam and replace its contents with:
import gleam/http
import gleam/http/response
import gleam/erlang/process
import counter

pub fn main() {
  let counter_pid = counter.start()

  http.serve(
    http.new_server()
    |> http.handle("/", fn(_req) {
      let me = process.self()
      process.send(counter_pid, counter.Get(me))
      let count = process.receive(fn(c) { c })

      response.ok("Counter: " <> Int.to_string(count))
    })
    |> http.handle("/inc", fn(_req) {
      process.send(counter_pid, counter.Inc)
      response.ok("Incremented!")
    }),
    3000,
  )
}

▶️ Run the App
gleam run

🌍 Use the App
•	View counter:
👉 http://localhost:3000/
•	Increment counter:
👉 http://localhost:3000/inc
Refresh / to see the updated value.

📚 What You Learn
•	Gleam syntax and types
•	Message passing
•	State management with processes
•	Building HTTP servers on BEAM

🛠 Possible Improvements
•	Add decrement route
•	Return JSON responses
•	Add HTML buttons
•	Persist counter value

📜 License
MIT License

