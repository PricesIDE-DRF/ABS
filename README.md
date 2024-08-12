# ABS
Abstract Behavioral Specification (ABS) is a formal modeling language and framework designed for specifying and analyzing distributed systems. It combines object-oriented and concurrent programming concepts with formal methods, offering features such as object-oriented modeling, concurrent programming, functional programming elements, and support for formal verification. ABS is characterized by its modular design, which facilitates separation of concerns and modular reasoning.

In the context of Software Product Line Engineering (SPLE), ABS provides valuable tools for managing variability and developing families of related software products. Its delta-oriented programming approach allows for flexible implementation of product variations, while its support for feature models and product line configurations enables the specification of valid feature combinations and automatic product generation. ABS's formal foundations permit verification of properties across entire product lines, ensuring correctness across all variants. 

This repository showcased the study case for using ABS in context of Delta oriented programming with payment gateway as its topic. 
## How to run
### Running on Browser-Based IDE
- Run this docker image
  `docker run -d --rm -p 8080:80 --name collaboratory abslang/collaboratory:latest`
- Open localhost:8080
- Paste the code snippet on the browser

### Running on local
- Clone the repo
- Run this command
  `java -jar "path/to/absfrontend.jar" "path/to/[file_name].abs"`
