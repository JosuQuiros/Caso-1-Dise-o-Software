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
![Ventana Login](Imagenes/Wireframes/Login.png)
![Heatmap Login](Imagenes/Heatmaps/Login.jpg)

#### Select Folder
Upload interface that allows the user to choose the source path of the documents; includes language selectors and a slider control to define the minimum confidence level required by the AI. 
![Seleccionar Folder](Imagenes/Wireframes/SeleccionarDocumento.png)
![Heatmap SelectDocument](Imagenes/Heatmaps/SelectDocument.jpg)
#### Select DUA Template
Selection panel that displays the official variants of the form (Import, Export, etc.) through interactive cards and a preview of the corresponding `.docx` format.  
![Seleccionar la plantilla de DUA](Imagenes/Wireframes/SeleccionarPlantilla.png)
![Heatmap SelectTemplate](Imagenes/Heatmaps/SelectTemplate.jpg)
#### How to Monitor Process Progress
Real-time dashboard that breaks down the stages of extraction and semantic analysis, using progress bars and low-confidence alerts for each processed file.  
![Monitoreo de avance](Imagenes/Wireframes/ViewProcess.png)
![Heatmap ViewerProcess](Imagenes/Heatmaps/ViewerProcess.jpg)
#### How the Final Result Looks
Closing screen that presents the generated document with a traffic-light system (green/yellow/red) applied to the data, and enables the final download of the Word file ready for submission to the Ministry of Finance.  
![Resultado Final](Imagenes/Wireframes/Resultado.png)
![Heatmap Result](Imagenes/Heatmaps/Result.jpg)

## 1.3 Component design strategy
- Use Atomic Design methodology to structure UI components (atoms, molecules, organisms, templates, pages) in React.  
- Develop all components using React 19.2 with TypeScript 5.9.3 to ensure type safety and maintainability.  
- Encapsulate styles per component using CSS Modules to avoid style conflicts and improve scalability.  
- CSS class naming convention follows: ComponentName-StyleName.  
- Use relative units (em/rem) for layout and typography to support responsive design.  
- Components support internationalization using react-i18next (v16.5.8).  
- Ensure component reusability and testability through unit tests with Jest 30.2.0.  
- Validate component behavior and user interactions through integration/end-to-end testing using Playwright 1.58.2.  
- Follow a modular folder structure separating components, hooks, services, and utilities.  
- No specific accessibility (a11y) requirements are defined for this application.

## 1.4 Security
- Multi-Factor Authentication (MFA) through AWS Cognito.  
- Mobile authenticator application (TOTP) supported.  
- Single Sign-On (SSO) through AWS Cognito (with optional external identity providers).  
- Authentication is handled by AWS Cognito.  
  
- **Roles:**  
- Manager  
- Customs Agent  
### Permissions by Role  

#### Manager  
  
- **Permission Code:** MANAGE_USERS  
- **Description:** Manage user CRUD operations.  
  
- **Permission Code:** VIEW_REPORTS  
- **Description:** Access operational and performance reports.  
  
- **Permission Code:** EDIT_TEMPLATES  
- **Description:** Modify or update available DUA templates.  
  
#### Customs Agent  
  
- **Permission Code:** LOAD_FILES  
- **Description:** Upload folders containing required data files.  
  
- **Permission Code:** GENERATE_DUA  
- **Description:** Initiates the AI process to generate a DUA.  
  
- **Permission Code:** DOWNLOAD_DUA  
- **Description:** Download generated DUA documents.  
  
---  
  
- AWS Secrets Manager is used to store environment variables, API keys, and sensitive configuration data.  
- **Server Name:** `customs-identity-service`

## 1.5 Layered design

- The frontend supports Server-Side Rendering (SSR) using React 19 with a Node.js runtime.

- If there is no authenticated session, the Authentication Layer is invoked via AWS Cognito.

- If authentication is successful, the requested view is rendered within the Components Layer.

- Components follow Atomic Design (atoms, molecules, organisms, templates, and pages).  
  Within components, a Hooks Layer connects UI interactions with the Services Layer.

- The Services Layer contains the core business logic of the application.

- Services may require access to the following layers:
  - Utils (helper functions and shared utilities)
  - ApiClients (external API communication)
  - Settings (configuration management)

- ApiClients contains all modules responsible for interacting with external services (e.g., AWS services, third-party APIs).

- Settings retrieves environment variables and secrets from AWS Secrets Manager during runtime.

- ApiClients read API endpoints, credentials, and configuration values from the Settings layer.

- All ApiClient requests and responses are mapped using Models (TypeScript interfaces/types), which are validated using a Data Validation layer (e.g., Zod).

- All layers can access shared modules such as:
  - Models
  - Utils
  - State Management (e.g., Redux Toolkit or React Context)

- A Notification Service layer enables asynchronous communication between services using event-driven patterns (e.g., AWS SNS/SQS or callbacks).

- Asynchronous operations (e.g., long-running DUA generation processes) are handled via events and callbacks through the Notification Service.

- The Logging Layer provides centralized logging of system events and errors (e.g., AWS CloudWatch).

- The Exception Handling Layer is shared across all layers to standardize error management and responses.

---

### Architecture Diagram 

				+----------------------+  
				| User Browser |  
				+----------+-----------+  
						|  
						v  
				+---------------------------+  
				| AWS (CloudFront / ALB) |  
				| NodeJS + React SSR |  
				+----------+----------------+  
						|  
				SSR Request Handling  
						|  
				Authentication  
				(AWS Cognito)  
						|  
				+----------------------+  
				| Components Layer |  
				| Atomic Design UI |  
				| Atoms → Pages |  
				+----------+-----------+  
						|  
				Hooks  
						|  
				Services Layer  
						|  
				+----------+-----------+-----------+  
						| | | |  
				Utils ApiClients Settings State Mgmt  
						|  
				AWS Secrets Manager  
						|  
				Secrets / Config

---

ApiClients → External APIs / AWS Services  
External Services → Notification Service (Events / Callbacks)

Shared Layers:

- Models (TypeScript)
    
- Zod Validation
    
- Redux / Context API
    
- Exception Handling
    
- Logging (AWS CloudWatch)
    

CI/CD:  
GitHub Repository → GitHub Actions → Build & Test (Jest / Playwright) → Deploy (AWS: EC2 / ECS / Amplify)

## 1.6 Design patterns
- Use Builder Pattern and Strategy Pattern to create the diffrent document processors such as wordx, xlsx, pdf, jpg, png. 
- NotificationService subscriptions works with Obsever pattern
- Use adapter pattern to decide the output format to be writen in the documents, use FormatAdapters y Concret Format such as: Paragraph, Bullets, Table, Label, Amount. 
- Singleton for: ExceptionHandling, Document Parsers, Utils, StateManagement, The Api Clients, Settings classes. 
