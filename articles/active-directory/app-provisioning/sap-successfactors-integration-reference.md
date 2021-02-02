---
title: Diretório Ativo Azure e referência de integração SAP SuccessFactors
description: Mergulho técnico profundo no fornecimento orientado para o SAP SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256226"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Como o provisionamento do Azure Ative Directory se integra com os SAP SuccessFactors 

[O serviço de fornecimento de utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerir o ciclo de vida identitária dos utilizadores. O Azure Ative Directory oferece três integrações pré-construídas: 

* [SucessoFactors para fornecimento de utilizadores ativos no local](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SucessoFactors para o provisionamento de utilizadores do Azure Ative Directory](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SucessoFactors Writeback](../saas-apps/sap-successfactors-writeback-tutorial.md)

Este artigo explica como a integração funciona e como você pode personalizar o comportamento de provisionamento para diferentes cenários de RH. 

## <a name="establishing-connectivity"></a>Estabelecimento de conectividade 
O serviço de fornecimento de Azure AD utiliza a autenticação básica para ligar aos pontos finais da API central de OData do Empregado. Ao configurar a app de provisionamento successFactors, utilize o parâmetro *URL* do inquilino na secção *Credenciais Admin* para configurar o URL do [centro de dados API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Para garantir ainda mais a conectividade entre o serviço de fornecimento de AD Azure e os SuccessFactors, pode adicionar as gamas AD AD AD no SuccessFactors IP allow-list utilizando os passos descritos abaixo:

1. Descarregue os [mais recentes Intervalos IP](https://www.microsoft.com/download/details.aspx?id=56519) para a Nuvem Pública do Azure 
1. Abra o ficheiro e procure a etiqueta **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Gama Azure AD IP](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copie todas as gamas de endereços IP listadas no endereço de *elementoPrefixos* e use o intervalo para construir a sua lista de restrições de endereço IP.
1. Traduza os valores CIDR para os intervalos IP.  
1. Inicie sessão no portal de administração SuccessFactors para adicionar intervalos IP à lista de admissões. Consulte a [nota de suporte sap 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Agora pode [introduzir gamas IP](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) nesta ferramenta. 

## <a name="supported-entities"></a>Entidades apoiadas
Para cada utilizador em SuccessFactors, o serviço de fornecimento AZure AD recupera as seguintes entidades. Cada entidade é expandida utilizando o parâmetro de consulta $expand OData *API.* Consulte a coluna *de regras de recuperação* abaixo. Algumas entidades são expandidas por padrão, enquanto algumas entidades são expandidas apenas se um atributo específico estiver presente no mapeamento. 

| \# | Entidade de SucessoFactors                  | Nó OData     | Regra de recuperação |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *nó de raiz*                  | Sempre           |
| 2  | PerPersonal                            | personalInfoNav              | Sempre           |
| 3  | PerPhone                               | telefoneNav                     | Sempre           |
| 4  | Peremail                               | e-mailNav                     | Sempre           |
| 5  | EmpEmployment                          | empregoNav                | Sempre           |
| 6  | User                                   | empregoNav/userNav        | Sempre           |
| 7  | EmpJob                                 | empregoNav/jobInfoNav     | Sempre           |
| 8  | EmpEmploymentTermination               | activeEmpresocount       | Sempre           |
| 9  | Gestor do utilizador                         | empregoNav/userNav/manager/empInfo | Sempre  |
| 10 | FOCompany                              | empregoNav/jobInfoNav/companyNav | Apenas se `company` ou `companyId` atributo for mapeado |
| 11 | FODepartment                           | empregoNav/jobInfoNav/departmentNav | Apenas se `department` ou `departmentId` atributo for mapeado |
| 12 | FOBusinessUnit                         | empregoNav/jobInfoNav/businessUnitNav | Apenas se `businessUnit` ou `businessUnitId` atributo for mapeado |
| 13 | FOCostCenter                           | empregoNav/jobInfoNav/costCenterNav | Apenas se `costCenter` ou `costCenterId` atributo for mapeado |
| 14 | FODivision                             | empregoNav/jobInfoNav/divisionNav  | Apenas se `division` ou `divisionId` atributo for mapeado |
| 15 | FOJobCode                              | empregoNav/jobInfoNav/jobCodeNav  | Apenas se `jobCode` ou `jobCodeId` atributo for mapeado |
| 16 | FOPayGrade                             | empregoNav/jobInfoNav/payGradeNav  | Só se `payGrade` o atributo for mapeado |
| 17 | FOLocation                             | empregoNav/jobInfoNav/locationNav  | Só se `location` o atributo for mapeado |
| 18 | FOCorporateAddressDEFLT                | empregoNav/jobInfoNav/addressNavDEFLT  | Se o mapeamento contiver um dos seguintes atributos: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | empregoNav/jobInfoNav/eventReasonNav  | Só se `eventReason` o atributo for mapeado |
| 20 | EmpGlobalAssignment                    | empregoNav/empGlobalAssignmentNav | Só se `assignmentType` for mapeado |
| 21 | Lista de Picklist de Mente De Emprego                | empregoNav/jobInfoNav/employmentTypeNav | Só se `employmentType` for mapeado |
| 22 | Lista de Picklist classe de empregados                 | empregoNav/jobInfoNav/employeeClassNav | Só se `employeeClass` for mapeado |
| 23 | EmplStatus Picklist                    | empregoNav/jobInfoNav/emplStatusNav | Só se `emplStatus` for mapeado |
| 24 | Lista de Picklist deType de atribuição                | empregoNav/empGlobalAssignmentNav/assignmentTypeNav | Só se `assignmentType` for mapeado |

## <a name="how-full-sync-works"></a>Como funciona a sincronização completa
Com base no mapeamento de atributos, durante o serviço de fornecimento de Ad Azure em sincronização completa envia a seguinte consulta "GET" OData API para obter dados eficazes de todos os utilizadores ativos. 

> [!div class="mx-tdCol2BreakAll"]
>| Parâmetro | Descrição |
>| ----------|-------------|
>| Anfitrião OData API | Anexar https ao URL do *Inquilino.* Exemplo: `https://api4.successfactors.com` |
>| Ponto final da OData API | `/odata/v2/PerPerson` |
>| OData $format parâmetro de consulta | `json` |
>| OData $filter parâmetro de consulta | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand parâmetro de consulta | Este valor de parâmetro depende dos atributos mapeados. Exemplo: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize parâmetro de consulta | `100` |

> [!NOTE]
> Durante a primeira sincronização completa inicial, o serviço de fornecimento de Azure AD não retira dados de trabalhador inativos/terminados.

Para cada utilizador SuccessFactors, o serviço de fornecimento procura uma conta no alvo (Azure AD/on-premises Ative Directory) utilizando o atributo correspondente definido no mapeamento. Por exemplo: se *o personIdExternal mapear* para *o employeeId* e for definido como o atributo correspondente, então o serviço de fornecimento utiliza o valor *personIdExternal* para procurar o utilizador com filtro *Staff empregado.* Se for encontrada uma correspondência de utilizador, atualiza os atributos-alvo. Se não for encontrado fósforo, cria uma nova entrada no alvo. 

Para validar os dados devolvidos pelo seu ponto final OData API para uma `personIdExternal` especificação, atualize a `SuccessFactorsAPIEndpoint` consulta abaixo na consulta API abaixo com o URL do seu servidor de data center API e utilize uma ferramenta como o [Carteiro](https://www.postman.com/downloads/) para invocar a consulta. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Como funciona a sincronização incremental

Após uma sincronização completa, o serviço de fornecimento AZure AD `LastExecutionTimestamp` mantém-no e utiliza-o para criar consultas delta para recuperar alterações incrementais. Os atributos da timetamp presentes em cada entidade SuccessFactors, `lastModifiedDateTime` `startDate` como, `endDate` `latestTerminationDate` e, são avaliados para ver se a mudança se enquadra entre o `LastExecutionTimestamp` e . `CurrentExecutionTime` . Se sim, então a alteração de entrada é considerada eficaz e processada para sincronização. 

## <a name="reading-attribute-data"></a>Dados de atributos de leitura

Quando o serviço de fornecimento de Azure AD consulta o SuccessFactors, recupera um conjunto de resultados JSON. O conjunto de resultados JSON inclui uma série de atributos armazenados na Central dos Empregados. Por predefinição, o esquema de provisionamento está configurado para recuperar apenas um subconjunto desses atributos. 

Para obter atributos adicionais, siga os passos listados abaixo:
    
1. Navegue para **aplicações empresariais**  ->  **SuccessFactors App**  ->  **Provisioning**  ->  **Editing Edição Página** de  ->  **mapeamento de atributos**.
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique na **lista de atributos Editar para SuccessFactors**. 

   > [!NOTE] 
   > Se a lista de atributos Editar para a opção **SuccessFactors** não aparecer no portal Azure, utilize o URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para aceder à página. 

1. A coluna **de expressão API** nesta vista mostra as expressões JSONPath utilizadas pelo conector.

   >[!div class="mx-imgBorder"] 
   >![Expressão API](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Pode editar um valor JSONPath existente ou adicionar um novo atributo com uma expressão JSONPath válida ao esquema. 

A secção seguinte fornece uma lista de cenários comuns para a edição dos valores JSONPath. 

## <a name="handling-different-hr-scenarios"></a>Lidar com diferentes cenários de RH

JSONPath é uma linguagem de consulta para JSON que é semelhante a XPath para XML. Tal como o XPath, o JSONPath permite a extração e filtração de dados a partir de uma carga útil JSON.

Ao utilizar a transformação JSONPath, pode personalizar o comportamento da app de provisionamento Azure AD para recuperar atributos personalizados e lidar com cenários como a recontratar, a conversão de trabalhadores e a atribuição global. 

Esta secção abrange como pode personalizar a aplicação de provisionamento para os seguintes cenários de RH: 
* [Recuperação de atributos adicionais](#retrieving-additional-attributes)
* [Recuperação de atributos personalizados](#retrieving-custom-attributes)
* [Lidar com o cenário de conversão dos trabalhadores](#handling-worker-conversion-scenario)
* [Lidar com o cenário de recontratar](#handling-rehire-scenario)
* [Lidar com o cenário de atribuição global](#handling-global-assignment-scenario)
* [Lidar com cenário de emprego simultâneo](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Recuperação de atributos adicionais

O padrão Azure AD SuccessFactors fornecendo navios de esquema de aplicativos com [mais de 90 atributos pré-definidos](sap-successfactors-attribute-reference.md). Para adicionar mais atributos SuccessFactors ao esquema de provisionamento, utilize os passos listados abaixo: 

1. Utilize a consulta OData abaixo para obter dados para um utilizador de teste válido da Central do Empregado. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Determinar a entidade Central dos Trabalhadores associada ao atributo
   * Se o atributo faz parte da entidade *EmpEmployment,* procure o atributo no nó *de EmpregoNav.* 
   * Se o atributo faz parte da entidade *utilizadora,* procure o atributo no nó *de EmpregoNav/userNav.*
   * Se o atributo faz parte da entidade *EmpJob,* procure o atributo no *nó de EmpregoNav/jobInfoNav.* 
1. Construa o Caminho JSON associado ao atributo e adicione este novo atributo à lista de atributos SuccessFactors. 
   * Exemplo 1: Digamos que quer adicionar o atributo *okToRehire,* que faz parte da entidade *employmentNav,* em seguida, use o JSONPath  `$.employmentNav.results[0].okToRehire`
   * Exemplo 2: Digamos que pretende adicionar o *timeZone* do atributo, que faz parte da entidade *userNav,* e depois use o JSONPath `$.employmentNav.results[0].userNav.timeZone`
   * Exemplo 3: Digamos que quer adicionar o atributo *flsaStatus,* que faz parte da entidade *jobInfoNav,* e depois use o JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Salve o esquema. 
1. Reinicie o provisionamento.

### <a name="retrieving-custom-attributes"></a>Recuperação de atributos personalizados

Por padrão, os seguintes atributos personalizados são pré-definidos na app de provisionamento Azure AD SuccessFactors: 
* *personalizado15* da entidade User (userNav)
* *customString1-customString15* da entidade EmpEmployment (employmentNav) chamada *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* da entidade EmpJobInfo (jobInfoNav) chamada *empJobNavCustomString1-empNavJobCustomString15*

Digamos que, no seu exemplo central de empregados, o atributo *customString35* no *EmpJobInfo* armazena a descrição da localização. Pretende fluir este valor para o atributo Ative *Directory physicalDeliveryOfficeName.* Para configurar o mapeamento de atributos para este cenário, utilize os passos abaixo: 

1. Editar a lista de atributos SuccessFactors para adicionar um novo atributo chamado *empJobNavCustomString35*.
1. Desloque a expressão JSONPath API para este atributo como: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Guarde e recarregue a alteração de mapeamento no portal Azure.  
1. Na lâmina de mapeamento de atributos, *mapear o empJobNavCustomString35* para o *físicoDeliveryOfficeName*.
1. Guarde o mapeamento.

Ampliação deste cenário: 
* Se quiser mapear o atributo *personalizado 35* da entidade *utilizadora,* utilize o JSONPath `$.employmentNav.results[0].userNav.custom35`
* Se quiser mapear o atributo *CustomString35* da entidade *EmpEmployment,* use o JSONPath `$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Lidar com o cenário de conversão dos trabalhadores

A conversão do trabalhador é o processo de conversão de um trabalhador a tempo inteiro para um empreiteiro ou um empreiteiro a tempo inteiro. Neste cenário, a Employee Central adiciona uma nova entidade *EmpEmployment* juntamente com uma nova entidade *utilizadora* para a mesma entidade *Pessoa.* A entidade *utilizadora* aninhada sob a entidade *empEmployment* anterior está definida como nula. Para lidar com este cenário de modo a que os novos dados do emprego apareçam quando ocorre uma conversão, pode atualizar em massa o esquema de aplicações de provisionamento utilizando os passos listados abaixo:  

1. Abra a lâmina de mapeamento de atributos da sua aplicação de provisionamento SuccessFactors. 
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas. 

   >![A screenshot mostra o seu esquema aqui link que abre o editor de esquemas.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar. 

   >![A screenshot mostra o editor de Schema com Download selecione para guardar uma cópia do esquema.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
1. Na caixa de texto encontrar, copiar e colar o valor `$.employmentNav.results[0]`
1. Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[?(@.userNav != null)]` . Note o espaço branco que rodeia o `!=` operador, que é importante para o processamento bem sucedido da expressão JSONPath. 
   >![encontrar substituir-conversão](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Clique na opção "substituir todos" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
   * Velho JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Reinicie o provisionamento. 

### <a name="handling-rehire-scenario"></a>Lidar com o cenário de recontratar

Normalmente existem duas opções para processar recontratar recontratadas: 
* Opção 1: Criar um novo perfil de pessoa na Central de Funcionários
* Opção 2: Reutilizar o perfil da pessoa existente na Central dos Empregados

Se o seu processo de RH utilizar a Opção 1, não são necessárias alterações ao esquema de provisionamento. Se o seu processo de RH utilizar a Opção 2, então a Employee Central adiciona uma nova entidade *EmpEmployment* juntamente com uma nova entidade *utilizadora* para a mesma entidade *Pessoa.* Ao contrário do cenário de conversão, a entidade *utilizadora* na anterior entidade *empEmployment* não está definida como nula. 

Para lidar com este cenário de recontratar (opção 2), de modo a que os dados mais recentes sobre o emprego apareçam para recontratar perfis, pode atualizar em massa o esquema de aplicações de provisionamento utilizando os passos listados abaixo:  

1. Abra a lâmina de mapeamento de atributos da sua aplicação de provisionamento SuccessFactors. 
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas.   
1. Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar.   
1. No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
1. Na caixa de texto encontrar, copiar e colar o valor `$.employmentNav.results[0]`
1. Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[-1:]` . Esta expressão JSONPath devolve o mais recente registo *empEmployment.*   
1. Clique na opção "substituir todos" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
   * Velho JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Reinicie o provisionamento. 

Esta alteração de esquema também apoia o cenário de conversão dos trabalhadores. 

### <a name="handling-global-assignment-scenario"></a>Lidar com o cenário de atribuição global

Quando um utilizador na Central de Empregados é processado para atribuição global, o SuccessFactors adiciona uma nova entidade *EmpEmployment* e define a *atribuiçãoClass* para "GA". Também cria uma nova entidade *utilizadora.* Assim, o utilizador tem agora:
* Uma entidade *utilizadora empEmployment*  +   que corresponde à atribuição de casa com *atribuiçãoClass* definido para "ST" e 
* Outra entidade *utilizadora empEmployment*  +   que corresponde à atribuição global com *atribuiçãoClass* definido para "GA"

Para obter atributos pertencentes à atribuição padrão e ao perfil de utilizador de atribuição global, utilize os passos listados abaixo: 

1. Abra a lâmina de mapeamento de atributos da sua aplicação de provisionamento SuccessFactors. 
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas.   
1. Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar.   
1. No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
1. Na caixa de texto encontrar, copiar e colar o valor `$.employmentNav.results[0]`
1. Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Clique na opção "substituir todos" para atualizar o esquema. 
1. Salve o esquema. 
1. O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
   * Velho JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Novo JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Recarregue a lâmina de mapeamento de atributos da aplicação. 
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique na **lista de atributos Editar para SuccessFactors**.
1. Adicione novos atributos para obter dados de atribuição global. Por exemplo: se quiser obter o nome do departamento associado a um perfil de atribuição global, pode adicionar o atributo *globalPartamento de Assinaturas* com a expressão JSONPath definida para `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Agora pode fluir ambos os valores do departamento para atributos ative directory ou fluir seletivamente um valor usando o mapeamento de expressão. Exemplo: a expressão abaixo define o valor do atributo do *departamento* de AD ao *globalAssignmentDepartment,* caso seja presente, outros define o valor para o *departamento* associado à atribuição padrão. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Guarde o mapeamento. 
1. Reinicie o provisionamento. 

### <a name="handling-concurrent-jobs-scenario"></a>Lidar com cenário de emprego simultâneo

Quando um utilizador na Central de Empregados tem empregos simultâneos/múltiplos, existem duas entidades *empEmployment* e *Utilizador* com *atribuiçãoClass* definida para "ST". Para obter atributos pertencentes a ambos os trabalhos, utilize os passos listados abaixo: 

1. Abra a lâmina de mapeamento de atributos da sua aplicação de provisionamento SuccessFactors. 
1. Desloque-se para baixo e clique **Mostrar Opções avançadas**.
1. Clique na **lista de atributos Editar para SuccessFactors**.
1. Digamos que quer retirar o departamento associado ao trabalho 1 e ao emprego 2. O *departamento* de atributos pré-definido já adquire o valor do departamento para o primeiro emprego. Pode definir um novo atributo chamado *secondJobDepartment* e definir a expressão JSONPath para `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Agora pode fluir ambos os valores do departamento para atributos ative directory ou fluir seletivamente um valor usando o mapeamento de expressão. 
1. Guarde o mapeamento. 
1. Reinicie o provisionamento. 

## <a name="writeback-scenarios"></a>Cenários de writeback

Esta secção abrange diferentes cenários de recuo. Recomenda abordagens de configuração com base na configuração do e-mail e do número de telefone nos SuccessFactors.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Cenários suportados para cancelamento de telefone e e-mail 

| \# | Requisito de cenário | E-mail primário <br> valor da bandeira | Telefone da empresa <br> valor da bandeira primária | Telemóvel <br> valor da bandeira primária | Telefone da empresa <br> mapeamento | Telemóvel <br> mapeamento |
|--|--|--|--|--|--|--|
| 1 | * Apenas definir o e-mail de negócios como principal. <br> Não estabeleça números de telefone. | true | true | false | \[Não definir\] | \[Não definir\] | 
| 2 | * No SuccessFactors, o e-mail de negócios e o telefone de negócios é primordial <br> * Flua sempre o número de telefone Azure AD para o telefone comercial e para o telemóvel. | true | true | false | telephoneNumber | dispositivo móvel | 
| 3 | * No SuccessFactors, o email de negócios e o telemóvel é primordial <br> * Flua sempre o número de telefone Azure AD para o telefone comercial e para o telemóvel para o telemóvel | true | false | true |  telephoneNumber | dispositivo móvel | 
| 4 | * No SuccessFactors o email de negócios é primário <br> * Em Azure AD, verifique se o número de telefone do trabalho está presente, se estiver presente, então verifique se o número de telemóvel também está presente, marque o número de telefone de trabalho como principal apenas se o número de telemóvel não estiver presente. | true | Use mapeamento de expressão: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Use mapeamento de expressão: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | dispositivo móvel | 
| 5 | * No SuccessFactors o email de negócios e o telefone de negócios é primordial. <br> * Em Azure AD, se o telemóvel estiver disponível, em seguida, desfiá-lo como telefone comercial, senão use o telefoneNumber. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Não definir\] | 

* Se não houver mapeamento para número de telefone no resmapeamento do atributo de rescamada, então apenas o e-mail é incluído na write-back.
* Durante o novo aluguer de bordo na Central de Empregados, o email de negócios e o número de telefone podem não estar disponíveis. Se definir o e-mail de negócios e o telefone comercial como principal é obrigatório durante o embarque, pode definir um valor falso para telefone comercial e e-mail durante a nova criação de aluguer, que acabará por ser atualizado pela app de write-back.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Cenários não suportados para cancelamento de telefone e e-mail

* Na Central dos Funcionários, durante o embarque de e-mails pessoais e telefone pessoal é definido como primário. A aplicação de write-back não pode mudar esta definição e definir o e-mail de negócios e o telefone comercial como principal.
* Na Central dos Empregados, o telefone comercial é definido como principal. A aplicação de write-back não pode alterar isto e definir o telemóvel como principal.
* A aplicação de write-back não pode ler as definições de bandeira primária atuais e usar os mesmos valores para a operação de escrita. Os valores de bandeira configurados no mapeamento do atributo serão sempre utilizados. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba como configurar o SuccessFactors para o provisionamento do Ative Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Saiba como configurar a reversão para o SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Saiba mais sobre atributos de Sucessos suportados para o provisionamento de entrada](sap-successfactors-attribute-reference.md)










