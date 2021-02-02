---
title: Referência de integração do Azure Ative Directory e workday
description: Mergulho técnico profundo no fornecimento orientado para o Workday-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: f260bca196839a091ae7d12be6d5f85912bf92db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255989"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Como o provisionamento do Diretório Ativo Azure se integra com o Workday

[O serviço de fornecimento de utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com [o Workday HCM](https://www.workday.com) para gerir o ciclo de vida identitária dos utilizadores. O Azure Ative Directory oferece três integrações pré-construídas: 

* [Dia útil para o provisionamento do utilizador ative do Diretório ativo](../saas-apps/workday-inbound-tutorial.md)
* [Dia de trabalho para o provisionamento do utilizador do Azure Ative Directory](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Repetição de escrita do Workday](../saas-apps/workday-writeback-tutorial.md)

Este artigo explica como a integração funciona e como você pode personalizar o comportamento de provisionamento para diferentes cenários de RH. 

## <a name="establishing-connectivity"></a>Estabelecimento de conectividade 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Restringir o acesso da API ao Azure AD
O serviço de fornecimento de Azure AD utiliza a autenticação básica para ligar aos pontos finais da API dos Serviços Web workday.  

Para garantir ainda mais a conectividade entre o serviço de fornecimento AZure AD e o Workday, pode restringir o acesso para que o utilizador do sistema de integração designado aceda apenas às APIs do Dia de Trabalho a partir das gamas IP AZURE AD permitidas. Por favor, envolva o seu administrador workday para completar a seguinte configuração no seu inquilino Workday. 

1. Descarregue os [mais recentes Intervalos IP](https://www.microsoft.com/download/details.aspx?id=56519) para a Azure Public Cloud. 
1. Abra o ficheiro e procure a etiqueta **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Gama Azure AD IP](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copie todas as gamas de endereços IP listadas no endereço de *elementoPrefixos* e use o intervalo para construir a sua lista de endereços IP.
1. Faça login no portal de administração workday. 
1. Aceda à tarefa **Keep IP Ranges** para criar uma nova gama IP para centros de dados Azure. Especifique as gamas IP (utilizando a notação CIDR) como uma lista separada por vírgula.  
1. Aceda à tarefa **'Gerir Políticas de Autenticação'** para criar uma nova política de autenticação. Na política de autenticação, utilize a lista de autenticação para especificar a gama IP Azure E o grupo de segurança que terá acesso a partir desta gama IP. Guarde as alterações. 
1. Aceda à tarefa **'Ativar todas as alterações de autenticação pendentes'** para confirmar alterações.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Limitar o acesso aos dados dos trabalhadores no Dia de Trabalho utilizando grupos de segurança limitados

Os passos predefinidos para [configurar o sistema de integração Workday](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) o utilizador concede acesso para recuperar todos os utilizadores no seu inquilino Workday. Em certos cenários de integração, poderá querer limitar o acesso, de modo a que os utilizadores pertencentes apenas a determinadas organizações de supervisão sejam devolvidos pela Get_Workers chamada da API e processados pelo conector Azure AD do Workday. 

Pode cumprir este requisito trabalhando com o seu administrador workday e configurando grupos de segurança do sistema de integração restrita. Para obter mais informações sobre como isto é feito, consulte [este artigo da comunidade workday](https://community.workday.com/forums/customer-questions/620393) *(as credenciais de login da Comunidade workday são necessárias para aceder a este artigo)*

Esta estratégia de limitar o acesso utilizando issg restrito (Grupos de Segurança do Sistema de Integração) é particularmente útil nos seguintes cenários: 
* **Cenário de lançamento faseado**: Você tem um grande inquilino workday e planeia realizar um lançamento faseado do Workday para Azure AD provisão automatizado. Neste cenário, em vez de excluir os utilizadores que não estão no âmbito da fase atual com filtros de deteção AD Azure, recomendamos configurar o ISSG constrangido para que apenas os trabalhadores em campo sejam visíveis para a Azure AD.
* **Cenário de empregos de a provisionamento múltiplos**: Você tem um grande inquilino workday e vários domínios de AD cada um apoiando uma unidade de negócio/divisão/empresa diferente. Para apoiar esta topologia, você gostaria de executar vários dias de trabalho para Azure AD provisionando empregos com cada emprego fornecendo um conjunto específico de trabalhadores. Neste cenário, em vez de utilizar filtros de deteção AZure AD para excluir dados dos trabalhadores, recomendamos configurar o ISSG constrangido para que apenas os dados relevantes do trabalhador seja visível para a Azure AD. 

### <a name="workday-test-connection-query"></a>Consulta de conexão de teste do dia de trabalho

Para testar a conectividade com o Workday, a Azure AD envia o seguinte *pedido de serviços* web Get_Workers workday. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Como funciona a sincronização completa

**A sincronização total** no contexto do provisionamento orientado para o dia de trabalho refere-se ao processo de busca de todas as identidades do Dia de Trabalho e à determinação das regras de provisionamento aplicáveis a cada objeto do trabalhador. A sincronização completa acontece quando liga o provisionamento pela primeira vez e também quando reinicia o *provisionamento* a partir do portal Azure ou a utilização de APIs de gráfico. 

A Azure AD envia o seguinte *Get_Workers* pedido de Serviços Web workday para recuperar dados do trabalhador. A consulta procura o registo de transação workday para todas as entradas efetivas datadas do trabalhador a partir do momento correspondente à execução completa do sync. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
O *nó Response_Group* é usado para especificar quais os atributos do trabalhador para obter do Dia de Trabalho. Para obter uma descrição de cada bandeira no nó *Response_Group,* consulte a documentação do Workday [Get_Workers API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType). 

Determinados valores de bandeira especificados no *nó Response_Group* são calculados com base nos atributos configurados no pedido de provisionamento Azure AD do Workday. Consulte a secção sobre *entidades apoiadas* para os critérios utilizados para definir os valores de bandeira. 

A *resposta Get_Workers* do Workday para a consulta acima inclui o número de registos de trabalhadores e contagem de páginas.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Para recuperar a próxima página do conjunto de resultados, a consulta *de Get_Workers* seguinte especifica o número da página como um parâmetro no *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
O serviço de fornecimento de AD AZure processa cada página e itera através de todos os trabalhadores eficazes durante a sincronização completa. Para cada entrada de trabalhador importado do dia de trabalho:
* A [expressão XPATH](workday-attribute-reference.md) é aplicada para recuperar valores de atributos do Workday.
* Aplicam-se as regras de mapeamento e correspondência do atributo e 
* O serviço determina qual a operação a executar no alvo (Azure AD/AD). 

Uma vez concluído o processamento, guarda o carimbo de tempo associado ao início da sincronização completa como marca de água. Esta marca de água serve como ponto de partida para o ciclo de sincronização incremental. 

## <a name="how-incremental-sync-works"></a>Como funciona a sincronização incremental

Após uma sincronização completa, o serviço de fornecimento de Azure AD `LastExecutionTimestamp` mantém-no e utiliza-o para criar consultas delta para recuperar alterações incrementais. Durante a sincronização incremental, a Azure AD envia os seguintes tipos de consultas para o Workday: 

* [Consulta para atualizações manuais](#query-for-manual-updates)
* [Consulta para atualizações e rescisões efetivas](#query-for-effective-dated-updates-and-terminations)
* [Consulta para contratações futuras](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Consulta para atualizações manuais

No *seguinte, Get_Workers* solicitar consultas para atualizações manuais que ocorreram entre a última execução e o tempo de execução atual. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Consulta para atualizações e rescisões efetivas

Os *seguintes Get_Workers* solicitar consultas para atualizações efetivas datadas que ocorreram entre a última execução e o tempo de execução atual. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Consulta para contratações futuras

Se alguma das consultas acima retorna uma contratação futura, então o seguinte *Get_Workers* pedido é usado para obter informações sobre uma nova contratação datada futura. O atributo *WID* do novo aluguer é usado para realizar o lookup e a data efetiva é definida para a data e hora de aluguer. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Recuperação de atributos de dados dos trabalhadores

A *Get_Workers* API pode devolver diferentes conjuntos de dados associados a um trabalhador. Dependendo das [expressões XPATH API](workday-attribute-reference.md) configuradas no esquema de provisionamento, o serviço de fornecimento Azure AD determina quais os conjuntos de dados a obter do Dia de Trabalho. Assim, as bandeiras *Response_Group* estão colocadas no *pedido Get_Workers.* 

A tabela abaixo fornece orientações sobre a configuração de mapeamento para usar para recuperar um conjunto de dados específico. 

| \# | Entidade do dia de trabalho                       | Incluído por padrão | Padrão XPATH para especificar no mapeamento para buscar entidades não-padrão             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Dados Pessoais                        | Sim                 | wd:Dados do \_ Trabalhador/wd:Dados Pessoais \_                                             |
| 2  | Dados do Emprego                      | Sim                 | wd:Dados dos \_ trabalhadores/wd:Dados do emprego \_                                           |
| 3  | Dados adicionais de trabalho                  | Sim                 | wd:Dados do \_ Trabalhador/wd:Dados de \_ emprego/wd:Trabalho \_ \_ dos Trabalhadores \[ @wd:Primary \_ Emprego=0\]|
| 4  | Dados da Organização                    | Sim                 | wd:Dados dos \_ trabalhadores/wd:Dados da organização \_                                         |
| 5  | Dados da cadeia de gestão                | Sim                 | wd:Dados dos \_ trabalhadores/wd:Dados da cadeia de gestão \_ \_                                    |
| 6  | Organização de Supervisão             | Sim                 | «SUPERVISÃO»                                                                 |
| 7  | Empresa                              | Sim                 | 'EMPRESA'                                                                     |
| 8  | Unidade de Negócio                        | Não                  | 'UNIDADE DE \_ NEGÓCIO'                                                              |
| 9  | Hierarquia da Unidade de Negócios              | Não                  | «HIERARQUIA \_ DA UNIDADE DE \_ NEGÓCIO»                                                   |
| 10 | Hierarquia da Empresa                    | Não                  | "HIERARQUIA \_ DA EMPRESA"                                                          |
| 11 | Centro de Custos                          | Não                  | 'COST \_ CENTER'                                                                |
| 12 | Hierarquia do Centro de Custos                | Não                  | 'HIERARQUIA DO \_ CENTRO \_ DE CUSTOS'                                                     |
| 13 | Fundo                                 | Não                  | 'FUNDO'                                                                        |
| 14 | Hierarquia do Fundo                       | Não                  | "HIERARQUIA \_ DO FUNDO"                                                             |
| 15 | Presente                                 | Não                  | 'PRESENTE'                                                                        |
| 16 | Hierarquia do Presente                       | Não                  | 'HIERARQUIA \_ DO PRESENTE'                                                             |
| 17 | Concessão                                | Não                  | 'SUBVENÇÃO'                                                                       |
| 18 | Hierarquia grant                      | Não                  | "HIERARQUIA \_ DE SUBVENÇÕES"                                                            |
| 19 | Hierarquia do Site de Negócios              | Não                  | "HIERARQUIA \_ DO SITE DE \_ NEGÓCIOS"                                                   |
| 20 | Organização Matrix                  | Não                  | 'MATRIX'                                                                      |
| 21 | Grupo De Pagamento                            | Não                  | 'PAY \_ GROUP'                                                                  |
| 22 | Programas                             | Não                  | 'PROGRAMAS'                                                                    |
| 23 | Hierarquia do Programa                    | Não                  | 'HIERARQUIA \_ DO PROGRAMA'                                                          |
| 24 | Region                               | Não                  | «HIERARQUIA DA \_ REGIÃO»                                                           |
| 25 | Hierarquia de Localização                   | Não                  | «HIERARQUIA \_ DA LOCALIZAÇÃO»                                                         |
| 26 | Dados de Provisão de Conta            | Não                  | wd:Dados do \_ Trabalhador/wd:Dados \_ de Provisionamento de Conta \_                                |
| 27 | Dados de verificação de antecedentes                | Não                  | wd:Dados do \_ trabalhador/wd:Dados de verificação de \_ \_ antecedentes                                    |
| 28 | Benefício Dados de Elegibilidade             | Não                  | wd:Dados do \_ Trabalhador/wd:Dados \_ de elegibilidade do benefício \_                                 |
| 29 | Dados de Inscrição de Benefícios              | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de inscrição de \_ \_ benefícios                                  |
| 30 | Dados de Carreira                          | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de carreira \_                                               |
| 31 | Dados de Compensação                    | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de compensação \_                                         |
| 32 | Dados da Autoridade Tributária Do Trabalhador Contingente | Não                  | wd:Dados do \_ Trabalhador/wd:Autoridade \_ \_ Tributária Contingente \_ Formulário \_ \_ Dados do Tipo \_       |
| 33 | Dados de artigos de desenvolvimento                | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de produto de desenvolvimento \_ \_                                    |
| 34 | Dados dos Contratos de Trabalhadores              | Não                  | wd:Dados do \_ Trabalhador/wd:Dados \_ dos contratos de trabalho \_                                  |
| 35 | Dados de Revisão dos Colaboradores                 | Não                  | wd:Dados dos \_ trabalhadores/wd:Dados de revisão dos \_ \_ trabalhadores                                     |
| 36 | Feedback Dados Recebidos               | Não                  | wd:Dados do \_ Trabalhador/wd:Feedback \_ \_ Dados recebidos                                   |
| 37 | Dados do objetivo do trabalhador                     | Não                  | wd:Dados do \_ Trabalhador/wd:Dados da meta do trabalhador \_ \_                                         |
| 38 | Dados fotográficos                           | Não                  | wd:Dados do \_ Trabalhador/wd:Dados \_ fotográficos                                                |
| 39 | Dados de Qualificação                   | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de qualificação \_                                        |
| 40 | Dados de Pessoas Relacionadas                 | Não                  | wd:Dados do \_ Trabalhador/wd:Dados \_ de pessoas \_ relacionadas                                     |
| 41 | Dados de Função                            | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de função \_                                                 |
| 42 | Dados de Competências                           | Não                  | wd:Dados do \_ Trabalhador/wd:Dados de \_ competências                                                |
| 43 | Dados do perfil de sucessão              | Não                  | wd:Dados do \_ trabalhador/wd:Dados de perfil de sucessão \_ \_                                  |
| 44 | Dados de Avaliação de Talentos               | Não                  | wd:Dados dos \_ trabalhadores/wd:Dados \_ de avaliação de \_ talentos                                   |
| 45 | Dados da Conta do Utilizador                    | Não                  | wd:Dados do \_ Trabalhador/wd:Dados da conta do utilizador \_ \_                                        |
| 46 | Dados do Documento do Trabalhador                 | Não                  | wd:Dados do \_ Trabalhador/wd:Dados do documento do trabalhador \_ \_                                     |

>[!NOTE]
>Cada entidade do Dia de Trabalho listada na tabela está protegida por uma **Política de Segurança de Domínio** no Dia de Trabalho. Se não conseguir recuperar qualquer atributo associado à entidade após a definição do XPATH certo, consulte o seu administrador workday para garantir que a política de segurança de domínio adequada está configurada para o utilizador do sistema de integração associado à aplicação de provisionamento. Por exemplo, para recuperar *dados de habilidades,* *obtém-se* acesso no domínio do trabalho *Dados do Trabalhador: Competências e Experiência*. 

Aqui estão alguns exemplos sobre como pode alargar a integração do Workday para satisfazer requisitos específicos. 

**Exemplo 1**

Digamos que pretende recuperar os seguintes conjuntos de dados do Workday e usá-los nas suas regras de provisionamento:

* Centro de custos
* Hierarquia do centro de custos
* Grupo de pagamento

Os conjuntos de dados acima não estão incluídos por padrão. Para recuperar estes conjuntos de dados:
1. Faça login no portal Azure e abra o seu Workday para a aplicação de provisionamento de utilizadores AD/Azure. 
1. Na lâmina de provisionamento, edite os mapeamentos e abra a lista de atributos Workday a partir da secção avançada. 
1. Adicione as seguintes definições de atributos e marque-as como "Necessárias". Estes atributos não serão mapeados a nenhum atributo em AD ou Azure AD. Servem apenas como sinais para o conector para recuperar as informações do Cost Center, Cost Center Hierarchy e Pay Group. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome do atributo | Expressão API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[='Organization_Type_ID']='COST_CENTER_HIERARCHY'] @wd:type/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() @wd:type |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() @wd:type |

1. Uma vez que o conjunto de dados do Cost Center e do Pay Group esteja disponível na resposta *Get_Workers,* pode utilizar os valores de XPATH abaixo para recuperar o nome do centro de custos, o código do centro de custos e o grupo de pagamento. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome do atributo | Expressão API XPATH |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[='Cost wd:Organization_Type_Reference/@wd:Descriptor Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[='Cost wd:Organization_Type_Reference/@wd:Descriptor Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[='Pay wd:Organization_Type_Reference/@wd:Descriptor Group']/wd:Organization_Name/text() |

**Exemplo 2**

Digamos que pretende obter certificações associadas a um utilizador. Esta informação está disponível como parte do conjunto de *Dados de Qualificação.* Para obter este conjunto de dados como parte da resposta *Get_Workers,* utilize o seguinte XPATH: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Exemplo 3**

Digamos que quer recuperar *grupos de provisionamento atribuídos* a um trabalhador. Estas informações estão disponíveis como parte do conjunto de *Dados de Provisionamento de Conta.* Para obter este conjunto de dados como parte da resposta *Get_Workers,* utilize o seguinte XPATH: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Passos seguintes

* [Saiba como configurar o Workday para o provisionamento do Ative Directory](../saas-apps/workday-inbound-tutorial.md)
* [Saiba como configurar escrever de volta ao Workday](../saas-apps/workday-writeback-tutorial.md)
* [Saiba mais sobre atributos de trabalho suportados para o provisionamento de entrada](workday-attribute-reference.md)

