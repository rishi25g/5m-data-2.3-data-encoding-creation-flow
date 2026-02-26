# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: Implement a simple Thrift server and client that defines a `Student` struct with fields `name` (string), `age` (integer), and `courses` (list of strings). Include a service `School` with a method `enrollCourse` that takes a `Student` record and a course name, adds the course to the student's course list, and returns the updated `Student` record.

Answer:

```python
# Thrift schema (student.thrift)
%%writefile ../schema/student.thrift

Student {
  1: required string Name,
  2: optional i64 age,
  3: optional list<string> courses
}

service School {
    Student enrollCourse(1: required Student student, 2: required string course)
}

# Thrift server (student_server.py)

%%writefile ../student_server.py
import thriftpy2
student_thrift = thriftpy2.load("./schema/student.thrift", module_name="student_thrift")

from thriftpy2.rpc import make_server # call this server to createserver

class School(object):
    def enrollCourse(self, student, course):
        student.interests.append(course)
        return student

server = make_server(student_thrift.School, School(), client_timeout=None)
print("Starting and running the server...")
print("Press Ctrl+C to stop the server.")
server.serve()

# Thrift client (student_client.py)
import thriftpy2
student_thrift = thriftpy2.load("../schema/student.thrift", module_name="student_thrift")

from thriftpy2.rpc import make_client

school = make_client(student_thrift.School, timeout=None)

```
python student_thrift_server.py

### Question 2

Question: Implement a simple Protocol Buffers server and client that defines a `Book` message with fields `title` (string), `author` (string), and `page_count` (integer). Include a service `Library` with a method `checkoutBook` that takes a `Book` message and returns the same `Book` message.

Answer:

```python
# Protobuf schema (book.proto)
%%writefile ../schema/book.proto
syntax = "proto3";

message Book {
  string title = 1;
  string author = 2;
  optional int64 page_count = 3;
}


service Library {
  rpc CheckoutBook(Book) returns (Book) {}
}
# python -m grpc_tools.protoc -I./schema --python_out=. --grpc_python_out=. ./schema/book.proto

# Protobuf server (book_server.py)
%%writefile ../book_protobuf_server.py
from concurrent import futures
import grpc
import book_pb2_grpc


class library(book_pb2_grpc.LibraryServicer):
  def checkoutBook(self, request, context):
        print(f"Received book checkout request: {request}")
        return request

server = grpc.server(futures.ThreadPoolExecutor(max_workers=8))
book_pb2_grpc.add_LibraryServicer_to_server(Library(), server)
server.add_insecure_port('[::]:50051')
print("Starting and running the server...")
print("Press Ctrl+C to stop the server.")
server.start()
server.wait_for_termination()

  

# python book_protobuf_server.py 

# Protobuf client (book_client.py)
import sys
sys.path.append('..')
import grpc
import book_pb2
import book_pb2_grpc

def enrollCourse(stub, student, course):
    book = stub.enrollCourse(book_pb2.checkoutBook(book=book, checkout=checkout))
    return book


with grpc.insecure_channel('localhost:50051') as channel:
    book = book_pb2.Book(title='singapore history', author=raffles, page_count=208)
    checkout = "singapore history"
    stub = book_pb2_grpc.SchoolStub(channel)
    book = enrollCourse(stub, book, checkout)
    print(book.checkout)

```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
