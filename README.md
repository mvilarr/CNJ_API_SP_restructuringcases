## Using the National Council of Justice (CNJ) API

### -> About This Project

This project aims to collect and analyze data on judicial reorganization proceedings in the State of São Paulo, Brazil, through procedural communications published in the National Electronic Judicial Gazette (DJEN), created by the Brazilian National Council of Justice (CNJ) in 2016 to gradually replace the state-level official judicial gazettes.

The period analyzed is June 2025 to June 2026. The original intention was to collect historical data, but records prior to June 2025 were excluded because the São Paulo State Court of Justice (TJSP) migrated to the DJEN in July 2025.

According to information provided by the press offices of the TJSP and the CNJ, there is no deadline for all courts to adopt the DJEN, nor are they required to make procedural communications published before their migration available in the system. Currently, 72 of Brazil's 94 courts have adopted the DJEN, according to the CNJ's dashboard.

Procedural communications issued by the TJSP before June 2025 are available through the state's official judicial gazette at: https://www.tjsp.jus.br/atc/dejesp/.

### -> Main Findings

During the period analyzed (June 2025–June 2026), I identified 42 judgments in judicial reorganization proceedings, 35 of which dismissed the case. The reasons for dismissal were:

12 cases due to missing required documentation;
8 cases due to non-payment of court fees;
7 cases in which the company voluntarily withdrew its petition;
4 cases due to procedural issues;
2 cases because the company was not operating or had not been operating long enough to meet the legal requirements; and
1 case because the type of debt was not eligible for judicial reorganization proceedings.

### -> Data Collection

Accessing the DJEN requires using a VPN configured to appear as if the user is located in Brazil.

In the search bar, I entered the term "recuperação judicial" ("judicial reorganization"), which returns all procedural communications from every type of proceeding that mentions this expression. I then filtered the institution field to include only the São Paulo State Court of Justice (TJSP), thereby limiting the results to communications issued by that court.

By default, searches return only five results per page, and there is no indication of the total number of pages available. By inspecting the website using DevTools, it is possible to identify an undocumented API. Although the website provides its own API, requests are subject to limitations.

The API allows searches by day, but it does not indicate how many items will be returned per page, making it impossible to specify that parameter beforehand. In Python, I used a while True loop to address this limitation: as long as a page returned results, the script appended the request's output to the cases list.

Before making the requests, I used Python's calendar library to determine the number of days in each month, allowing me to perform daily requests. Within the same loop, I also created folders to save the data by month. However, I later regretted converting the JSON files directly into CSV format during this process because the data were nested. As a result, the destinatarios column—which contains the names of companies and their attorneys on the active side of the proceeding (polo ativo) was stored as a list of dictionaries.

### -> Data Cleaning

To resolve the issues caused by converting nested JSON data into CSV format, I converted the strings back into lists and extracted only the company names listed on the active side of the proceeding (polo A).

The "texto" column contains the full text of each procedural communication, but a small number of records still included HTML tags. I created a function to normalize these entries using BeautifulSoup, extracting only the plain text.

After cleaning the data, I filtered the "codigoClasse" column to include only code 129, which corresponds to judicial reorganization proceedings according to the CNJ's classification table (https://www.cnj.jus.br/wp-content/uploads/2011/02/tabela-de-classes-justia-estadual.pdf).

The initial dataset of nearly 240,000 records was reduced to approximately 20,000. Because I was interested only in final decisions, I filtered the "tipoDocumento" column to include only "sentença" (judgments), reducing the dataset to 42 cases.

I read each judgment in its entirety and individually reviewed the underlying court proceedings (https://eproc-consulta.tjsp.jus.br/consulta_1g/externo_controlador.php?acao=tjsp@consulta_unificada_publica/consultar&hash=ed2215016033e517baaf4ff37bd4c428). I also consulted basic company information through Brazil's federal business registry website (https://consultacnpj.redesim.gov.br/).
