# Scaling AI from PoC to MVP: A Blueprint for MVP-RAG-Based LLM Systems

## Intro:
In 2022, [Gartner](https://www.gartner.com/en/newsroom/press-releases/2022-08-22-gartner-survey-reveals-80-percent-of-executives-think-automation-can-be-applied-to-any-business-decision) revealed that approximately 54% of AI initiatives surveyed among 699 executives fail to transition from the Proof of Concept(PoC) stage to production. This statistic underscores a significant challenge in scaling AI technologies—a sentiment echoed by one of Gartner's VPs. This high rate of attrition highlights the complexities involved in moving from a controlled, experimental environment to production-ready solutions that deliver real value.

This document distills personal insights and lessons learned from firsthand experiences in developing and scaling MVP-RAG-Based LLM Systems, offering a structured blueprint for overcoming common hurdles that I experienced adding months to our time-to-market. By focusing on strategic modularity, interoperability, and operational efficiency, I aim to provide a practical framework that leverages Azure services. In hopes that you the reader gain value and quickly go from MVE to MVP.

## Blueprint and Lessons Learned
Our initial pipeline design, while adequate for experimental stages, proved problematic when scaling. Originally, our approach was somewhat monolithic, comprising various interconnected phases without clear separations. This structure led to significant delays and complications. Learning from this, we pivoted to a [Feature/Training/Inference (FTI) architecture](https://www.hopsworks.ai/post/mlops-to-ml-systems-with-fti-pipelines), emphasizing:

1. **Modularity**: Breaking down the pipeline into distinct, manageable components—Feature Pipeline, Training Pipeline, and Inference Pipeline—facilitates better management and scalability.
2. **Interoperability**: Establishing clear interfaces between components enhances transparency and collaboration across teams.
3. **Flexibility**: Each component can independently adopt the most suitable technology stack, allowing for easier updates and optimizations.
4. **Independence**: Components can be deployed, scaled, and monitored separately, improving operational efficiency.
5. **Adaptability**: The feature pipeline's design accommodates both batch and streaming data, ensuring the system can evolve without significant rework.

These refinements not only streamlined our development process but also fortified our LLMOps practices.

![Blueprint for MVP-RAG-Based LLM Systems](https://github.com/armansalimi-microsoft/MVP-RAG-Based-LLM-System-Framework/assets/150470041/6fd34a00-57fe-47b7-b694-c9244558507a)


## 1. Data Collection Pipeline
- **Tailored ETL Processes**: Recognizing the distinct characteristics of each data source, we devised unique Extract, Transform, Load (ETL) processes. This bespoke approach ensured optimal handling and preprocessing of data, irrespective of its origin.
- **Advanced Web Crawling and Parsing**: Utilizing [Selenium](https://www.selenium.dev/) for sophisticated web crawling and [BeatifulSoup](https://beautiful-soup-4.readthedocs.io/en/latest/) for precise HTML parsing, we efficiently cleaned and normalized the extracted data. The processed data was then stored in MongoDB/Cosmo DB, serving as a centralized repository for subsequent operations.
- **Change Data Capture (CDC) Integration**: By implementing a [Change Data Capture (CDC) pattern](https://www.confluent.io/blog/how-change-data-capture-works-patterns-solutions-implementation/), we established a real-time monitoring mechanism for database activities. This system automatically detects CRUD operations, triggering events in RabbitMQ/Queue Storage. This mechanism not only streamlined data synchronization efforts but also facilitated immediate responsiveness to data changes, significantly enhancing the agility of our feature pipeline.

## 2. Feature Pipeline
- **Seamless Data Synchronization**: Upon detection of new or modified documents in MongoDB/Cosmo DB, corresponding events are generated and queued. The Feature Pipeline, attentive to this queue, processes these events, ensuring the Vector Database (AI Search) remains synchronized with MongoDB/Cosmo DB. This real-time processing eliminates discrepancies between databases, ensuring data consistency.
- **Transition to Streaming Pipeline**: Embracing the CDC methodology allowed us to evolve from a traditional batch ETL pipeline (Functions) to a more dynamic, streaming pipeline. This shift mitigated the complexities associated with batch processing, such as identifying data deltas, and enhanced our system’s efficiency in handling continuous data flows.
- **Pipeline Decoupling**: By leveraging [Bytewax](https://github.com/bytewax/bytewax)/Functions/Promptflow for streaming data processing, our Feature Pipeline operates independently of data generation sources. This decoupling has scaled our architecture, enabling easy integration of new data sources by adhering to a standardized interface.
- **Data Cleaning and Formatting**: Critical to our process is the meticulous cleaning and formatting of raw data to fit our predefined schema, ensuring the high quality and usability of data throughout our system.

## 3. Feature Store
- **Data Preparation for RAG and LLM**: Our Feature Store is structured to accommodate both non-embedded (cleaned) and embedded (chunked) data snapshots. This strategic storage supports diverse uses, from prompt and answer generation for training to RAG implementation, facilitating seamless transitions between different stages of data processing and utilization.
- **Schema-Driven Data Management**: The architecture of our Feature Store is designed to adapt to various data source types, guiding how data is chunked, embedded, and subsequently loaded into the Vector Database (AI Search). This flexibility ensures optimal data representation for our retrieval-augmented generation tasks and LLM fine-tuning (once you are post-MVP, really consider fine-tuning).
- **Version Control and Data Consistency**: Maintaining version control within the Feature Store was critical for ensuring data integrity over time. We implement rigorous protocols to track changes and manage data versions, which helps in preventing data drift.

## 4 & 5. Training and Inference Pipelines
- **Holistic Evaluation with Large LLMs**: Incorporating the largest available LLM for automated evaluations proved invaluable, offering comprehensive insights at negligible additional costs. This strategy significantly contributed to the quality and efficacy of our model assessments.
- **Experimental Flexibility**: Our system’s design facilitates straightforward experimentation with different LLM configurations. This flexibility led to surprising discoveries, such as the effectiveness of smaller LLMs, underscoring the importance of a broad evaluative scope in identifying optimal model setups.
- **Post-MVP Enhancements**: Following our MVP, we embarked on fine-tuning our LLM model while maintaining our RAG framework. This dual approach allowed us to refine our system’s performance further. Additionally, model quantization was implemented to cater to diverse usage patterns, optimizing both speed and memory efficiency.
- **Optimization Best Practices**: In the inference phase, prioritizing the LLM’s speed and memory footprint became a guiding principle. Post-registration model quantization emerged as a crucial optimization step, balancing performance with computational resource management effectively.
- **Rigorous Monitoring and Compliance**: It is crucial to implement comprehensive logging, metric capture, and data lineage tracking within the Inference Pipeline. This approach not only facilitates ongoing monitoring and optimization of model performance but also ensures adherence to regulatory compliance standards. The regulatory compliance part was a big learned lesson for us...By systematically recording data provenance and transformations, we establish a transparent audit trail that can be invaluable during compliance reviews and for troubleshooting purposes.
- **Advanced LLMOps Practices**: Emphasize the deployment and ongoing management of LLMs using LLMOps principles to ensure smooth operations and efficient resource utilization. Automate model deployment processes, version control, and rollbacks to handle updates and iterations with minimal downtime.
- **Security Protocols**: Incorporate stringent security measures to protect data and model integrity. This includes implementing encryption in transit and at rest, using secure authentication methods, and conducting regular security audits.
- **Proactive Anomaly Detection**: Utilize anomaly detection systems to preemptively identify and mitigate unusual patterns or potential threats in real-time model performance.
- **Comprehensive Access Controls**: Establish granular access controls and role-based permissions within the Inference Pipeline to ensure that only authorized personnel have access to sensitive functions and data.
- **Detailed Performance Benchmarking**: Regularly measure the performance of the Inference Pipeline against established benchmarks and KPIs. We experienced a good deal of drift as our practices grew.





