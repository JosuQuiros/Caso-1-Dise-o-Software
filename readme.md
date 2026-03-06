# Dua streamliner design

The main problem addressed by the DUA Streamliner is the complexity and inefficiency involved in preparing the customs declaration form (DUA) for importers and exporters. Currently, relevant information is scattered across multiple document types such as Excel files, Word documents, PDFs, and scanned invoices. These documents often follow different formats and structures, requiring manual review and data entry. This process is time-consuming, prone to human error, and demands detailed knowledge of customs terminology and regulatory requirements, which increases operational costs and delays.

The proposed solution is an automated, AI-powered system that processes a single folder path provided by the user, containing all related documents regardless of format. The system will use multiformat reading engines to extract data from Excel and Word files, structured and unstructured text from PDFs, and apply advanced OCR to scanned images. Through semantic analysis powered by trained AI models specialized in customs terminology, it will contextually interpret and extract key data such as importer/exporter information, product descriptions, values (FOB/CIF), Incoterms, transport details, invoice data, country of origin, and applicable customs regime. The extracted information will then be automatically mapped to the official DUA template defined by the Ministry of Finance, validated for basic consistency, and flagged when ambiguity or low confidence is detected.

The expected results include a significant reduction in manual workload, fewer data-entry errors, and faster DUA preparation. The system will generate a fully pre-filled Word document (.docx) aligned with the official DUA template, using visual confidence indicators (green for high confidence, yellow for medium confidence, and red for fields requiring review). This approach enhances efficiency, improves compliance accuracy, and allows customs professionals to focus on validation and decision-making rather than repetitive administrative tasks.

# 1. Frontend design
## 1.1 Technology stack
- Application type: webapp
- Web framework: ReactJS version 19.2
- NodeJS version 21
- TypeScript 5.9.3
- Unit testing: Jest 30.2.0
- Integration testing: Playwright 1.58.2
- Cloud service: AWS
- Hosted by Amazon Elastic Beanstalk
- Code repositories: GitHub
- CI CD by GitHub actions 
- Enviroments: AWS Elastic Beanstalk Environments
- Observability by Wiston
- Zod 4.3.6 to data validation
- Prettier 3.8.1
- esLint 10.0.2
## 1.2 UX UI analysis
### Core business process
#### Login
1. User enters their login, password, and the one-time token.  
2. If the login attempt fails, a message is displayed: *invalid username or password*.  
3. If successful, access to the system is granted.  

#### Configure the Generator
1. Selection of folder with source documents (Excel, Word, PDF, scanned images).  
2. Configuration of reading and validation options (e.g., language, document type, required confidence level).  
3. Adjustment of specific rules according to the customs regime.  

#### Progress Monitoring
1. The system displays a progress panel with indicators for each stage (reading, extraction, semantic analysis, mapping to the DUA).  
2. Color codes or progress bars are used to facilitate understanding.  
3. Errors or low-confidence fields are notified in real time.  

#### Obtaining the Result
1. Automatic generation of a Word file (.docx) with the official format of the Ministry of Finance.  
2. Inclusion of visual confidence indicators (green, yellow, red).  
3. Option to download, review, and edit the document before official submission.  

#### Logout
1. The user selects the logout option.  
2. The system invalidates the token and closes the session.  
### Wireframes

#### Login Screen
This screen presents a centralized access form that integrates **Username**, **Password** (with visibility toggle option), and a specific field for the **OTP** (one-time token).  
It includes a **dynamic alert area** that only appears when invalid credentials are entered, prioritizing security and clarity before entering the system.  
![Ventana Login]([https://example.com/paisaje.jpg](https://lh3.googleusercontent.com/gg-dl/AOI_d__BAF7Bw9VkYuQ7AeTKuTmqx9_kwBwyWAbwqXfx1E_B_o7DEkSTNDfVUl_ON49Hb3zc4Oj4nhFCcPm7AvEKniBCfHF7Z41W05Zt2_dzSUe_i3xjjcAd6LbPGsZRhUSC9URcLZfgr10GHTQtsoUxAnASoIv5DUq_ryF00IyfZX9cbvb3Hw=s1024-rj))

#### Select Folder
Upload interface that allows the user to choose the source path of the documents; includes language selectors and a slider control to define the minimum confidence level required by the AI. 
![Seleccionar Folder](https://lh3.googleusercontent.com/gg-dl/AOI_d_8VJgNmjsQe9WBK4NW_-rC10EdM-FQJPRYZlSMVZkdQ3h6xJ_fXA7MJ7r8u7FGbriVShujXLzqJspoVXpovuXRnZymwe3rEuy6QMcQs5hjjGRS1btmaKbRBVjGa2ykOzAJk1csv8NAcrXlKGlzBN2Rl8SDYFKSWsv6E9IvgJ5ituMA6Rw=s1024-rj)
#### Select DUA Template
Selection panel that displays the official variants of the form (Import, Export, etc.) through interactive cards and a preview of the corresponding `.docx` format.  
![Seleccionar la plantilla de DUA](https://lh3.googleusercontent.com/gg-dl/AOI_d_-12bmMgvKlGIcQ3BCWbXJ3DhyKCLz4O_DI2wYmr3GQLu8rpyouqMgiqSCxYfyq0oL82u7FRT6jx3lEhv6ckZVuBWXWA0NdFyx2cerS584ZrrmCpdBbnLke2AgRXaJywxJddbmsaHCAF8jowCvnZgUR1BfYbyd5-P7NoWDRRrdg5IX9JA=s1024-rj)
#### How to Monitor Process Progress
Real-time dashboard that breaks down the stages of extraction and semantic analysis, using progress bars and low-confidence alerts for each processed file.  
![Monitoreo de avance](https://lh3.googleusercontent.com/gg-dl/AOI_d_8bEUY4RfOoUHE5tVxQyj5-as-fzp-ceuziZVKarFO2bsIco7khrZzu44tb64qzvtvdPjSGHs2bdgKbIKoX9VljzrPM-40CQcZnokL6nKd384bmLGvIWI8faEkOyzauNQe9OysLVl_7h2LRDNmXg5BVthGBDA8ITOlJlYjd52pfUpMy_A=s1024-rj)
#### How the Final Result Looks
Closing screen that presents the generated document with a traffic-light system (green/yellow/red) applied to the data, and enables the final download of the Word file ready for submission to the Ministry of Finance.  
![Resultado Final](https://lh3.googleusercontent.com/gg-dl/AOI_d_9iiQBIsrqBtNnCxsmOgH8wrNotWmR22fCLdPSg_J0cUmVxprUzQi1CFQ5w9Vm4SDA29odvuY0aTXERWNQoewhbeg5qALFBvT5_qKw5o5ceOSKCQ2yOIXQyL7MeA72gECAuc88NomwdYfmi0JhGQhCYDlZE0SKUQQawBUiZCoaOnJM3pA=s1024-rj)




## 1.3 Component design strategy


## 1.4 Security

## 1.5 Layered design

## 1.6 Design patterns

