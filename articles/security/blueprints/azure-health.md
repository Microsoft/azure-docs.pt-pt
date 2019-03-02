---
title: Esquema de análise do Estado de funcionamento do Azure
description: Orientações para implementar um plano de gráfico de análise de estado de funcionamento HIPAA/HITRUST
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: 509d529c7e860f4c46bc3368028a4e8d24812051
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242824"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Segurança do Azure e o esquema de conformidade - HIPAA/HITRUST Health Data and AI

## <a name="overview"></a>Descrição geral

**O Azure no Security and Compliance Blueprint - dados de estado de funcionamento HIPAA/HITRUST e AI oferece uma implementação de chave na mão de uma solução de IaaS e PaaS do Azure para demonstrar como ingerir, armazenar, analisar, interagir, identidade e implantar com segurança soluções com dados de estado de funcionamento ao mesmo tempo a capacidade de cumprir requisitos de conformidade do setor. O plano gráfico ajuda a acelerar a adoção da cloud e a utilização para os clientes com dados que são controlados.**

O Azure no Security and Compliance Blueprint - dados de estado de funcionamento HIPAA/HITRUST e AI esquema fornece ferramentas e orientações para ajudar a implementar um seguro, Health Insurance Portability e Accountability Act (HIPAA) e estado de funcionamento informações Trust Alliance (HITRUST) pronto ambiente de plataforma-como-serviço (PaaS) para ingerir, armazenar, analisar e interagir com os registos médicos pessoais e não pessoais num ambiente de cloud segura e com várias camadas, implementado como uma solução ponto-a-ponto. 

Solução IaaS demonstrará como migrar uma solução SQL com base no local para o Azure e implementar uma estação de trabalho de acesso privilegiado (PAW) para gerir de forma segura soluções e serviços baseados na nuvem. A base de dados do SQL Server do IaaS adiciona experimentação potencial dados são importados para uma VM de IaaS do SQL e que a VM utiliza MSI autenticados o acesso ao interagir um serviço PaaS de SQL Azure. Ambos os estes demonstra uma arquitetura de referência comuns e foi projetado para simplificar a adoção do Microsoft Azure. Esta arquitetura fornecida ilustra uma solução para satisfazer as necessidades das organizações que procuram uma abordagem com base na cloud para reduzir a carga e o custo da implementação.

![](images/components.png)

A solução foi concebida para consumir um conjunto de dados de exemplo formatado com o rápido cuidados de saúde interoperabilidade recursos (FHIR), um padrão em todo o mundo para a troca de informações de cuidados de saúde eletronicamente e armazená-los de forma segura. Os clientes podem, em seguida, utilizar o Azure Machine Learning Studio para tirar partido de ferramentas poderosas de business intelligence e análises para rever as predições efetuadas nos dados de exemplo. Como exemplo do tipo de experimentação que pode facilitar o Azure Machine Learning Studio, o plano gráfico inclui um conjunto de dados de exemplo, scripts e ferramentas para prever a duração da estadia de um doente num local hospital. 

Este esquema deve ser usado como uma base modular para os clientes que se ajustar às suas necessidades específicas, desenvolver novas do Azure Machine learning experimentações para resolver ambos os cenários de casos de utilização clínica e operacional. Ele foi projetado para ser seguras e em conformidade quando implantados; No entanto, os clientes são responsáveis por configurar corretamente o funções e implementar qualquer modificação. Tenha em atenção o seguinte:

-   Esta arquitetura fornece uma linha de base para ajudar os clientes a utilizar o Microsoft Azure num HITRUST e o ambiente da HIPAA.

-   Embora o plano gráfico foi concebido para estar alinhado com a HIPAA e HITRUST (por meio do quadro comum de segurança - CSF), ele não deve ser considerado em conformidade até certificada por um auditor externo por HIPAA e HITRUST, os requisitos de certificação.

-   Os clientes são responsáveis por conduzir as revisões de segurança e conformidade adequadas de qualquer solução criados com esta arquitetura básica.

## <a name="deploying-the-automation"></a>Implementar a automatização

- Para implementar a solução, siga as instruções fornecidas a [orientações de implantação](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md). 

- Para obter uma descrição geral de como funciona esta solução, veja este [vídeo](https://aka.ms/healthblueprintvideo) explicar e demonstrar a implementação.

- Perguntas frequentes podem ser encontrada na [FAQ](https://aka.ms/healthblueprintfaq) orientações.

-   **Diagrama da arquitetura.** O diagrama mostra a arquitetura de referência utilizada para o esquema e o exemplo a utilizar o cenário de caso.

-   [Extensão de IaaS](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) esta solução demonstra como migrar uma solução SQL com base no local para o Azure e implementar uma estação de trabalho de acesso com privilégios para gerir serviços cloud-bsed e soluções de forma segura. 

## <a name="solution-components"></a>Componentes da solução


A arquitetura básica é composta pelos seguintes componentes:

-   **[Modelo de risco](https://aka.ms/healththreatmodel)**  um modelo de ameaças abrangente é fornecido no formato de tm7 para utilização com o [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168), mostrando os componentes da solução, os dados fluem entre eles e a confiança limites. O modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema durante o desenvolvimento de componentes de Machine Learning Studio ou outras modificações.

-   **[Matriz de implementação do cliente](https://aka.ms/healthcrmblueprint)**  livro do Excel da Microsoft de uma lista os requisitos de HITRUST relevantes e explica como a Microsoft e o cliente são responsáveis por cada um de reunião.

-   **[Revisão do Estado de funcionamento.](https://aka.ms/healthreviewpaper)** A solução foi avaliada por Coalfire systems, Inc. A conformidade de estado de funcionamento (HIPAA e HITRUST) revisão e orientações para a implementação fornece um auditor\'revisão s da solução e considerações para transformar o plano gráfico para uma implementação de prontos para produção.

## <a name="architectural-diagram"></a>Diagrama da arquitetura


![](images/ra2.png)

## <a name="roles"></a>Funções


O esquema define duas funções para os utilizadores administrativos (operadores) e três funções para os utilizadores na gestão hospitalar e assistência aos pacientes. Uma função de sexta está definida para um auditor avaliar a compatibilidade com a HIPAA e outros regulamentos. Do Azure com base em função de controlo de acesso (RBAC) permite precisamente focada gestão de acesso para cada usuário da solução através de funções incorporadas e personalizadas. Ver [introdução ao controlo de acesso baseado em funções no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) e [funções incorporadas para controlo de acesso baseado em funções do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) para obter informações detalhadas sobre as RBAC, funções e permissões.

### <a name="site-administrator"></a>Administrador do site


O administrador do site é responsável pela subscrição do cliente do Azure. Controlar a implementação geral, mas não têm acesso a registos dos doentes.

-   Atribuições de função padrão: [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Atribuições de função personalizada: N/A

-   Âmbito: Subscrição

### <a name="database-analyst"></a>Analista de banco de dados

O analista de banco de dados administra a instância do SQL Server e base de dados.
Eles não têm acesso a registos dos doentes.

-   Atribuições de funções incorporadas: [Contribuinte da BD SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [contribuinte do SQL Server](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Atribuições de função personalizada: N/A

-   Âmbito: ResourceGroup

### <a name="data-scientist"></a>Cientista de dados


Cientista de dados opera o Azure Machine Learning Studio. Pode importar, exportar e gerir os dados e executar relatórios. Cientista de dados tem acesso aos dados dos pacientes, mas não tem privilégios administrativos.

-   Atribuições de funções incorporadas: [Contribuidor de Conta de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Atribuições de função personalizada: N/A

-   Âmbito: ResourceGroup

### <a name="chief-medical-information-officer-cmio"></a>Diretor de informação médica (CMIO)


O CMIO faz a ponte entre a informática/tecnologia e profissionais de cuidados de saúde numa organização de saúde. As suas obrigações incluem a utilização da análise para determinar se os recursos estão a ser alocados adequadamente dentro da organização.

-   Atribuições de funções incorporadas: Nenhuma

### <a name="care-line-manager"></a>Gestor de linha de cuidados de saúde


O Gestor de linha de cuidados de saúde está diretamente envolvido no cuidado dos doentes.
Esta função exige monitorizar o estado de doentes individuais, bem como garantir que existe pessoal disponível para responder às necessidades específicas de prestação de cuidados de saúde dos seus doentes. O Gestor de linha de cuidados de saúde é responsável por adicionar e atualizar os registos dos pacientes.

-   Atribuições de funções incorporadas: Nenhuma

-   Atribuições de função personalizada: Tem o privilégio para executar HealthcareDemo.ps1 fazer ambos os admissão do doente e proprietários.

-   Âmbito: ResourceGroup

### <a name="auditor"></a>Auditor


O auditor avalia a solução de conformidade. Eles não têm acesso direto à rede.

-   Atribuições de funções incorporadas: [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Atribuições de função personalizada: N/A

-   Âmbito: Subscrição

## <a name="example-use-case"></a>Caso de utilização de exemplo


O caso de utilização de exemplo incluído com esse plano gráfico ilustra como o plano gráfico pode ser utilizado para ativar o machine learning e análises em dados de estado de funcionamento na cloud. Contosoclinic é um pequeno hospital localizado nos Estados Unidos. A rede de hospital os administradores querem utilizar o Azure Machine Learning Studio melhor para prever a duração da estadia de um doente no momento da admittance, para aumentar a eficiência da carga de trabalho operacional e melhorar a qualidade dos cuidados de saúde pode fornecer.

### <a name="predicting-length-of-stay"></a>Prever a duração da estadia


O cenário de caso de utilização de exemplo utiliza o Azure Machine Learning Studio para prever a duração de um doente recentemente admitted do tempo de permanência ao comparar os detalhes de médicos sido extraídos às prende dos pacientes agregados dados históricos de pacientes anteriores.
O plano gráfico inclui um amplo conjunto de registos médicos anónimos para demonstrar os recursos de treinamento e preditivos da solução. Numa implementação de produção, os clientes usaria seus próprios registros para preparar a solução para predições mais precisas, que reflete os detalhes exclusivos de seu ambiente, instalações e pacientes.

### <a name="users-and-roles"></a>Os utilizadores e funções


**Administrador do site – Alex**

*E-mail: Alex\_SiteAdmin*

Tarefa de Alex é avaliar as tecnologias que podem reduzir a carga de gerenciamento de uma rede no local e reduzir os custos de gestão. Alex tem sido avaliar o Azure há algum tempo, mas tiver dificuldade configurar os serviços que ele tem de cumprir os requisitos de conformidade de HiTrust para armazenar os dados dos doentes na cloud. Alex selecionou ia de estado de funcionamento do Azure para implementar uma solução de estado de funcionamento de conformidade prontas, o que ainda tratada os requisitos para cumprir os requisitos de cliente para HiTrust.

**Cientista de dados – Debra**

*E-mail: Debra\_DataScientist*

Debra é responsável por uso e a criação de modelos que analisam os registos médicos proporcionar informações sobre o cuidado de pacientes. Debra utiliza o SQL e a linguagem de programação estatística R para criar seus modelos.

**Analista de banco de dados – Danny**

*E-mail: Danny\_DBAnalyst*

Danny é o contacto principal para qualquer coisa sobre o Microsoft SQL Server que armazena todos os dados dos pacientes para Contosoclinic. Danny é um administrador experiente do SQL Server, que recentemente tornou-se familiarizado com a base de dados do Azure SQL.

**Diretor de informação médica – Caroline**

Caroline está trabalhando com Chris o Gestor de linha de cuidado e Debra cientista de dados para determinar quais os fatores que afetam seus pacientes duração da estadia.
Caroline utiliza as previsões de solução de (LOS) a duração da estadia para determinar se os recursos estão a ser alocados adequadamente na rede hospital. Por exemplo, usando o dashboard fornecido nesta solução.

**Se importa com o Gestor de linha – Chris**

*E-mail: Chris\_CareLineManager*

Como a pessoa diretamente responsável por gerenciar a admissão do doente e as altas em Contosoclinic, Chris utiliza as previsões geradas pela solução LOS para se certificar de que o pessoal suficiente está disponíveis para fornecer cuidado aos pacientes, enquanto eles estão mantendo-se na recurso.

**Auditor – Han**

*E-mail: Han\_Auditor*

Han é um auditor de certificado que tenha a experiência de auditoria para ISO, SOC e HiTrust. Han foi contratado para rever a rede do Contosoclinc. Han pode rever o cliente responsabilidade matriz fornecida com a solução para garantir que o esquema e a solução de LOS podem ser usados para armazenam, processam e exibir os dados pessoais confidenciais.


## <a name="design-configuration"></a>Configuração de design


Esta seção detalha as configurações predefinidas e medidas de segurança incorporadas no esquema descrito para:

- **INGESTÃO** origens de dados não processados incluindo a origem de dados FHIR
- **ARQUIVO** informações confidenciais
- **ANALISAR** e prever os resultados
- **INTERAGIR** com os resultados e previsões de indisponibilidade
- **IDENTIDADE** gestão da solução
- **SEGURANÇA** ativada funcionalidades


## <a name="identity"></a>IDENTIDADE 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>O Azure Active Directory e o controlo de acesso baseado em funções (RBAC)


**Autenticação:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) é o Microsoft\'s multi-inquilino com base na cloud diretório e identidade do serviço de gestão. Todos os utilizadores para a solução foram criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados SQL.



-   Autenticação para a aplicação é executada com o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [O Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades da sua organização, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades da sua organização, e Investiga os incidentes suspeitos e tome as medidas adequadas para resolvê-los.

-   [Do Azure com base em função de controlo de acesso (RBAC)](/azure/role-based-access-control/role-assignments-portal) permite a gestão de acesso direcionado precisamente para o Azure. Acesso à subscrição está limitado ao administrador da subscrição e o acesso do Azure Key Vault é limitado ao administrador do site. Palavras-passe seguras (12 carateres no mínimos, pelo menos uma letra de canto superior/inferior, o número e o caráter especial) são necessárias.

-   Autenticação multifator é suportada quando o comutador - enableMFA é ativado durante a implementação.

-   As palavras-passe expirarem após 60 dias, quando o comutador - enableADDomainPasswordPolicy é ativado durante a implementação.

**Funções:**

-   A solução utiliza [funções incorporadas](/azure/role-based-access-control/built-in-roles) para gerir o acesso aos recursos.

-   Todos os utilizadores são atribuídos a funções incorporadas específicas por predefinição.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Dados armazenados no Cofre de chaves incluem:

    -   Chave do Application insight
    -   Chave de acesso de armazenamento de dados dos pacientes
    -   Cadeia de ligação dos pacientes
    -   Nome da tabela de dados dos pacientes
    -   Ponto final de serviço Web do Azure ML
    -   Chave de API de serviço do Azure ML

-   Políticas de acesso avançadas são configuradas de forma necessidade
-   Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos
-   Todas as chaves e segredos no Cofre de chaves tenham datas de expiração
-   Todas as chaves no Cofre de chaves estão protegidas por HSM \[tipo de chave = a chave RSA de 2048 protegidas de HSM bits\]
-   Todos os utilizadores/identidades são concedidas as permissões mínimas necessárias usando o controle de acesso baseado em ' (RBAC) da função
-   Aplicações não partilham um Key Vault, a menos que confiam uns aos outros e eles precisarem de acesso para os segredos mesmo no tempo de execução
-   Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
-   Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios

## <a name="ingest"></a>INGESTÃO 

### <a name="azure-functions"></a>Funções do Azure
A solução foi projetada para usar [as funções do Azure](/azure/azure-functions/) para processar o comprimento de exemplo de dados mantenha-se usada na demonstração analytics. Três capacidades nas funções foram criadas.

**1. Importação em massa de dados de phi de dados do cliente**

Ao utilizar o script de demonstração. . \\HealthcareDemo.ps1 com o **BulkPatientAdmission** mudar conforme descrito na **implementar e executar a demonstração** executa o pipeline de processamento seguintes:
1. **Armazenamento de Blobs do Azure** -exemplo de ficheiro. csv de dados de pacientes carregadas para o armazenamento
2. **Grelha de eventos** -dados de eventos publica a função do Azure (importação em massa - eventos de BLOBs)
3. **Função do Azure** , realiza o processamento e armazena os dados para o armazenamento de SQL usando a função de segurança, eventos (do tipo; url do blob)
4. **BD SQL** - o armazenamento de banco de dados para dados dos doentes com etiquetas para classificação e o processo de ML é inicializado para fazer a experimentação de preparação.

![](images/dataflow.png)

Além disso, a função do azure foi concebida para ler e proteger dados confidenciais designados no conjunto de dados de exemplo com as seguintes tags:
- dataProfile => “ePHI”
- proprietário = > \<UPN de administrador do Site\>
- ambiente = > "Piloto"
- departamento = > "Ecossistema Global", a marcação foi aplicada ao conjunto de dados de exemplo em que o paciente "nomes" foi identificada como texto não encriptado.

**2. Admissão dos doentes novo**

Ao utilizar o script de demonstração. . \\HealthcareDemo.ps1 com o **BulkPatientadmission** mudar conforme descrito na **implementar e executar a demonstração** executa o pipeline de processamento seguintes: ![](images/securetransact.png)
**1. Função do Azure** acionada e a função de pedidos para um [token de portador](/rest/api/) do Azure Active directory.

**2. Key Vault** pedida para um segredo associado ao token do pedido.

**3. Funções do Azure validar o pedido e autorizar o pedido de acesso ao Key Vault.

**4. Key Vault** devolve o segredo, neste caso, a cadeia de ligação de BD SQL.

**5. Função do Azure** utiliza a cadeia de ligação para ligar em segurança à base de dados SQL e continua o processamento adicional para armazenar dados de ePHI.

Para alcançar o armazenamento dos dados, um esquema de API comum foi implementado seguindo rápida de cuidados de saúde de recursos de interoperabilidade (FHIR, pronunciado fire). A função foi fornecida os elementos de exchange FHIR seguintes:

-   [Esquema de pacientes](https://www.hl7.org/fhir/patient.html) abrange "quem" informações sobre um paciente.

-   [Esquema de observação](https://www.hl7.org/fhir/observation.html) abrange o elemento central nos cuidados de saúde, usado para oferecer suporte a diagnósticos, monitorizar o progresso, determinar as linhas de base e padrões de e até mesmo capturar demográficas características. 

-   [Encontrar o esquema](https://www.hl7.org/fhir/encounter.html) casa de bastidores os tipos de encontros como ambulatory, emergência, estado de funcionamento, impaciente e encontros virtual.

-   [Esquema de condição](https://www.hl7.org/fhir/condition.html) aborda informações detalhadas sobre uma condição, problema, diagnóstico, ou outros eventos, situação, problema ou clinical conceito que tem risen para um nível de preocupação.  



### <a name="event-grid"></a>Event Grid


A solução suporta o Azure Event Grid, um único serviço para gerir o encaminhamento de todos os eventos a partir de qualquer origem, para qualquer destino, desde que:

-   [Segurança e autenticação](/azure/event-grid/security-authentication)

-   [Controlo de acesso baseado em funções](/azure/event-grid/security-authentication#management-access-control) para várias operações de gestão, como a listagem subscrições de eventos, criar novas e geração de chaves

-   Auditoria

## <a name="store"></a>LOJA 

### <a name="sql-database-and-server"></a>Base de dados SQL e o servidor 


-   [Encriptação de dados transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) fornece encriptação em tempo real e a descriptografia dos dados armazenados no Azure SQL Database, usando uma chave armazenada no Azure Key Vault.

-   [Avaliação de vulnerabilidades do SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é fácil configurar a ferramenta que pode detetar, controlar e remediar potenciais vulnerabilidades das bases de dados.

-   [Deteção de ameaças da base de dados de SQL](/azure/sql-database/sql-database-threat-detection) ativada.

-   [A auditoria de base de dados SQL](/azure/sql-database/sql-database-auditing) ativada.

-   [Métricas de base de dados SQL e o registo de diagnósticos](/azure/sql-database/sql-database-metrics-diag-logging) ativada.

-   [Regras de firewall de nível do servidor e da base de dados](/azure/sql-database/sql-database-firewall-configure) ter sido fortalecida.

-   [Sempre encriptado colunas](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) são utilizadas para proteger seus pacientes, médio, nomes e apelidos.
    Além disso, a encriptação de coluna de base de dados também utiliza o Azure Active Directory (AD) para autenticar a aplicação à base de dados do Azure SQL.

-   Acesso à base de dados SQL e o SQL Server está configurado, de acordo com o princípio de privilégio mínimo.

-   Apenas os endereços IP necessários têm permissão para aceder através da firewall do SQL.

### <a name="storage-accounts"></a>Contas de armazenamento


-   [Os dados em movimento são transferidos utilizando TLS/SSL apenas](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Acesso anónimo não é permitido para contentores.

-   Regras de alerta são configuradas para controlar a atividade de anônima.

-   O HTTPS é necessário para aceder a recursos da conta de armazenamento.

-   Dados de pedidos de autenticação são iniciados e monitorizados.

-   Dados no armazenamento de BLOBs são encriptados em inatividade.

## <a name="analyze"></a>ANALISAR

### <a name="machine-learning"></a>Machine Learning


- [O registo está ativado](/azure/machine-learning/studio/web-services-logging) serviços da web para o Machine Learning Studio.
- Usando [Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) requer o desenvolvimento de experimentações que fornecem a capacidade de prever a um conjunto de solução.

## <a name="security"></a>SEGURANÇA

### <a name="azure-security-center"></a>Centro de Segurança do Azure
- [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) fornece uma visão centralizada do Estado de segurança dos seus recursos do Azure. Rapidamente, pode verificar que os controlos de segurança adequados estão implementados e corretamente configurado, e pode identificar rapidamente os recursos que requerem atenção. 

- [O Assistente do Azure](/azure/advisor/advisor-overview) é consultor da cloud personalizado que o ajuda a seguir as melhores práticas para otimizar as suas implementações do Azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

### <a name="application-insights"></a>Application Insights
- [O Application Insights](/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Deteta anomalias de desempenho. inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender o que os utilizadores efetivamente fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

### <a name="azure-alerts"></a>Alertas do Azure
- [Os alertas oferecem um método para monitorizar serviços do Azure e permitem-lhe configurar as condições em dados. Os alertas também fornecem notificações quando os dados de monitorização corresponde a uma condição de alerta.

### <a name="azure-monitor-logs"></a>Registos de Monitor do Azure
[Registos de Monitor do Azure](/azure/operations-management-suite/operations-management-suite-overview) é uma coleção de serviços de gestão.

-   Área de trabalho está ativada para o Centro de segurança

-   Área de trabalho está ativada para a carga de trabalho monitorização

-   Monitorização da carga de trabalho está ativada para:

    -   Identidade e Acesso

    -   Segurança e Auditoria

    -   Azure SQL DB Analytics

    -   [Análise de aplicação Web do Azure](/azure/log-analytics/log-analytics-azure-web-apps-analytics) solução

    -   Análise do Key Vault

    -   Monitorização de Alterações

-   [Conector do Application Insights (pré-visualização)](/azure/log-analytics/log-analytics-app-insights-connector) está ativada

-   [Log analytics da atividade](/azure/log-analytics/log-analytics-activity) está ativada
