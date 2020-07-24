---
title: Diretório Ativo Azure e referência de integração SAP SuccessFactors
description: Mergulho técnico profundo no fornecimento orientado para o SAP SuccessFactors-HR
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098377"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Como o provisionamento do Azure Ative Directory se integra com os SAP SuccessFactors 

[O serviço de fornecimento de utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) para gerir o ciclo de vida identitária dos utilizadores. O Azure Ative Directory oferece três integrações pré-construídas: 

* SucessoFactors para fornecimento de utilizadores ativos no local
* SucessoFactors para o provisionamento de utilizadores do Azure Ative Directory
* SucessoFactors Writeback

Este artigo explica como a integração funciona e como você pode personalizar o comportamento de provisionamento para diferentes cenários de RH. 

## <a name="establishing-connectivity"></a>Estabelecimento de conectividade 
O motor de provisionamento AD AD utiliza a autenticação básica para ligar aos pontos finais da API central de OData do Empregado. Ao configurar a app de provisionamento successFactors, utilize o parâmetro *URL* do inquilino na secção *Credenciais Admin* para configurar o URL do [centro de dados API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Para garantir ainda mais a conectividade entre o serviço de fornecimento de AD Azure e os SuccessFactors, pode adicionar as gamas AD AD AD no SuccessFactors IP allow-list utilizando os passos descritos abaixo:

* Descarregue os [mais recentes Intervalos IP](https://www.microsoft.com/download/details.aspx?id=56519) para a Nuvem Pública do Azure 
* Abra o ficheiro e procure as tags **AzureActiveDirectory** e **AzureActiveDirectoryDomainServices** 

  >[!div class="mx-imgBorder"] 
  >![Gama Azure AD IP](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Copie todas as gamas de endereços IP listadas no endereço de *elementoPrefixos* e use o intervalo para construir a sua lista de restrições de endereço IP.
* Traduza os valores CIDR para os intervalos IP.  
* Inicie sessão no portal de administração SuccessFactors para adicionar intervalos IP à lista de admissões. Consulte a [nota de suporte sap 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Agora pode [introduzir gamas IP](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) nesta ferramenta. 

## <a name="supported-entities"></a>Entidades apoiadas
Para cada utilizador em SuccessFactors, o serviço de fornecimento AZure AD recupera as seguintes entidades. Cada entidade é expandida utilizando o parâmetro de consulta $expand OData *API.* Consulte a coluna *de regras de recuperação* abaixo. Algumas entidades são expandidas por padrão, enquanto algumas entidades são expandidas apenas se um atributo específico estiver presente no mapeamento. 

| \# | Entidade de SucessoFactors                  | Nó OData     | Regra de recuperação |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *nó de raiz*                  | Sempre           |
| 2  | PerPersonal                            | personalInfoNav              | Sempre           |
| 3  | PerPhone                               | telefoneNav                     | Sempre           |
| 4  | Peremail                               | e-mailNav                     | Sempre           |
| 5  | EmpEmployment                          | empregoNav                | Sempre           |
| 6  | Utilizador                                   | empregoNav/userNav        | Sempre           |
| 7  | EmpJob                                 | empregoNav/jobInfoNav     | Sempre           |
| 8  | EmpEmploymentTermination               | activeEmpresocount       | Sempre           |
| 9  | FOCompany                              | empregoNav/jobInfoNav/companyNav | Só se o atributo empresa ou empresaId for mapeado |
| 10 | FODepartment                           | empregoNav/jobInfoNav/departmentNav | Só se o atributo departamento ou departamentoid for mapeado |
| 11 | FOBusinessUnit                         | empregoNav/jobInfoNav/businessUnitNav | Só se o atributo businessUnitId for mapeado |
| 12 | FOCostCenter                           | empregoNav/jobInfoNav/costCenterNav | Só se o atributo costCenter ou costCenterId for mapeado |
| 13 | FODivision                             | empregoNav/jobInfoNav/divisionNav  | Só se o atributo divisionId for mapeado |
| 14 | FOJobCode                              | empregoNav/jobInfoNav/jobCodeNav  | Só se o atributo JobCode ou jobCodeId estiver mapeado |
| 15 | FOPayGrade                             | empregoNav/jobInfoNav/payGradeNav  | Só se o atributo payGrade for mapeado |
| 16 | FOLocation                             | empregoNav/jobInfoNav/locationNav  | Só se o atributo de localização for mapeado |
| 17 | FOCorporateAddressDEFLT                | empregoNav/jobInfoNav/addressNavDEFLT  | Se o mapeamento contiver um dos seguintes atributos: officeLocationAddress, officeLocationCity, officeLocationZipCode |
| 18 | FOEventReason                          | empregoNav/jobInfoNav/eventReasonNav  | Só se o atributo EventReason for mapeado |
| 19 | EmpGlobalAssignment                    | empregoNav/empGlobalAssignmentNav | Só se a atribuiçãoType for mapeada |
| 20 | Lista de Picklist de Mente De Emprego                | empregoNav/jobInfoNav/employmentTypeNav | Só se o empregoType for mapeado |
| 21 | Lista de Picklist classe de empregados                 | empregoNav/jobInfoNav/employeeClassNav | Só se a Classe dos empregados for mapeada |
| 22 | EmplStatus Picklist                    | empregoNav/jobInfoNav/emplStatusNav | Só se o emplStatus for mapeado |
| 23 | Lista de Picklist deType de atribuição                | empregoNav/empGlobalAssignmentNav/assignmentTypeNav | Só se a atribuiçãoType for mapeada |

## <a name="how-full-sync-works"></a>Como funciona a sincronização completa
Com base no mapeamento do atributo, durante o serviço de fornecimento de Ad Azure de sincronização completa envia a seguinte consulta "GET" OData API para obter dados eficazes de todos os utilizadores ativos. 

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

Para validar os dados devolvidos pelo seu ponto final da OData API para uma *pessoa específicaIdExternal,* atualize o *Ponto De SucessoFactorsAPIEndpoint* na consulta API abaixo com o URL do seu servidor de data center da API e use uma ferramenta como o [Carteiro](https://www.postman.com/downloads/) para invocar a consulta. 

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

Após sincronização total, o serviço de fornecimento de Ad AZure mantém *o Último Tempo de Edição* e usa-o para criar consultas delta para recuperar alterações incrementais. Os atributos de datatamp presentes em cada entidade SuccessFactors, tais como *lastModifiedDateTime*, *startDate*, *endDate*, e *mais recenteTerminationDate*, são avaliados para ver se a mudança se insere entre o Último Tempo de *Ecologia* e *AtualExecutionTime*. Se sim, então a alteração de entrada é considerada eficaz e é processada para sincronização. 

## <a name="reading-attribute-data"></a>Dados de atributos de leitura

Quando o serviço de fornecimento de Azure AD consulta o SuccessFactors, recupera um conjunto de resultados JSON. O conjunto de resultados JSON inclui uma série de atributos armazenados na Central dos Empregados. Por predefinição, o esquema de provisionamento está configurado para recuperar apenas um subconjunto desses atributos. 

Para obter atributos adicionais, siga os passos listados abaixo:
    
* Navegue para **aplicações empresariais**  ->  **SuccessFactors App**  ->  **Provisioning**  ->  **Editing**  ->  **Dotado de Atribuição de Cartografia**.
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique na **lista de atributos Editar para SuccessFactors**. 

> [!NOTE] 
> Se a lista de atributos Editar para a opção **SuccessFactors** não aparecer no portal Azure, utilize o URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para aceder à página. 

* A coluna **de expressão API** nesta vista mostra as expressões JSONPath utilizadas pelo conector.
  >[!div class="mx-imgBorder"] 
  >![Expressão API](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* Pode editar um valor JSONPath existente ou adicionar um novo atributo com uma expressão JSONPath válida ao esquema. 

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

O padrão Azure AD SuccessFactors fornecendo navios de esquema de aplicativos com [mais de 90 atributos pré-definidos](sap-successfactors-attribute-reference.md). Para adicionar mais atributos de SuccessFactors fora da caixa ao esquema de provisionamento, utilize os passos listados abaixo: 

* Utilize a consulta OData abaixo para obter dados para um utilizador de teste válido da Central do Empregado. 

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

* Determinar a entidade Central dos Trabalhadores associada ao atributo
  * Se o atributo faz parte da entidade *EmpEmployment,* procure o atributo no nó *de EmpregoNav.* 
  * Se o atributo faz parte da entidade *utilizadora,* procure o atributo no nó *de EmpregoNav/userNav.*
  * Se o atributo faz parte da entidade *EmpJob,* procure o atributo no *nó de EmpregoNav/jobInfoNav.* 
* Construa o Caminho JSON associado ao atributo e adicione este novo atributo à lista de atributos SuccessFactors. 
  * Exemplo 1: Digamos que quer adicionar o atributo *okToRehire,* que faz parte da entidade *employmentNav,* em seguida, use o JSONPath`$.employmentNav.results[0].okToRehire`
  * Exemplo 2: Digamos que pretende adicionar o *timeZone*do atributo, que faz parte da entidade *userNav,* e depois use o JSONPath`$.employmentNav.results[0].userNav.timeZone`
  * Exemplo 3: Digamos que quer adicionar o atributo *flsaStatus,* que faz parte da entidade *jobInfoNav,* e depois use o JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Salve o esquema. 
* Reinicie o provisionamento.

### <a name="retrieving-custom-attributes"></a>Recuperação de atributos personalizados

Por padrão, os seguintes atributos personalizados são pré-definidos na app de provisionamento Azure AD SuccessFactors: 
* *personalizado15* da entidade User (userNav)
* *customString1-customString15* da entidade EmpEmployment (employmentNav) chamada *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* da entidade EmpJobInfo (jobInfoNav) chamada *empJobNavCustomString1-empNavJobCustomString15*

Digamos que, no seu exemplo central de empregados, o atributo *customString35* no *EmpJobInfo* armazena a descrição da localização. Pretende fluir este valor para o atributo Ative *Directory physicalDeliveryOfficeName.* Para configurar o mapeamento de atributos para este cenário, utilize os passos abaixo: 

* Editar a lista de atributos SuccessFactors para adicionar um novo atributo chamado *empJobNavCustomString35*.
* Desloque a expressão JSONPath API para este atributo como:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Guarde e recarregue a alteração de mapeamento no portal Azure.  
* Na lâmina de mapeamento do atributo, *mapear o empJobNavCustomString35* para *o físicoDeliveryOfficeName*.
* Guarde o mapeamento.

Ampliação deste cenário: 
* Se quiser mapear o atributo *personalizado 35* da entidade *utilizadora,* utilize o JSONPath`$.employmentNav.results[0].userNav.custom35`
* Se quiser mapear o atributo *CustomString35* da entidade *EmpEmployment,* use o JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Lidar com o cenário de conversão dos trabalhadores

A conversão do trabalhador é o processo de conversão de um trabalhador a tempo inteiro para um empreiteiro ou vice-versa. Neste cenário, a Employee Central adiciona uma nova entidade *EmpEmployment* juntamente com uma nova entidade *utilizadora* para a mesma entidade *Pessoa.* A entidade *utilizadora* aninhada sob a entidade *empEmployment* anterior está definida como nula. Para lidar com este cenário de modo a que os novos dados do emprego apareçam quando ocorre uma conversão, pode atualizar em massa o esquema de aplicações de provisionamento utilizando os passos listados abaixo:  

* Abra a lâmina de mapeamento de atributos da sua app de provisionamento SuccessFactors. 
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas. 
  >![revisão-esquema](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar. 
  >![download-schema](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
* Na caixa de texto encontrar, copiar e colar o valor`$.employmentNav.results[0]`
* Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[?(@.userNav != null)]` . Note o espaço branco que rodeia o `!=` operador, que é importante para o processamento bem sucedido da expressão JSONPath. 
  >![encontrar substituir-conversão](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Clique na opção "substituir todos" para atualizar o esquema. 
* Salve o esquema. 
* O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
  * Velho JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Novo JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Reinicie o provisionamento. 

### <a name="handling-rehire-scenario"></a>Lidar com o cenário de recontratar

Normalmente existem duas opções para processar recontratar recontratadas: 
* Opção 1: Criar um novo perfil de pessoa na Central de Funcionários
* Opção 2: Reutilizar o perfil da pessoa existente na Central dos Empregados

Se o seu processo de RH utilizar a Opção 1, não são necessárias alterações ao esquema de provisionamento. Se o seu processo de RH utilizar a Opção 2, então a Employee Central adiciona uma nova entidade *EmpEmployment* juntamente com uma nova entidade *utilizadora* para a mesma entidade *Pessoa.* Ao contrário do cenário de conversão, a anterior entidade *empEmployment* mantém a entidade *utilizadora* e não está definida como nula. 

Para lidar com este cenário de recontratar (opção 2), de modo a que os dados mais recentes sobre o emprego apareçam para recontratar perfis, pode atualizar em massa o esquema de aplicações de provisionamento utilizando os passos listados abaixo:  

* Abra a lâmina de mapeamento de atributos da sua app de provisionamento SuccessFactors. 
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas.   
* Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar.   
* No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
* Na caixa de texto encontrar, copiar e colar o valor`$.employmentNav.results[0]`
* Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[-1:]` . Esta expressão JSONPath devolve o mais recente registo *empEmployment.*   
* Clique na opção "substituir todos" para atualizar o esquema. 
* Salve o esquema. 
* O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
  * Velho JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Novo JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Reinicie o provisionamento. 

### <a name="handling-global-assignment-scenario"></a>Lidar com o cenário de atribuição global

Quando um utilizador na Central de Empregados é processado para atribuição global, o SuccessFactors adiciona uma nova entidade *EmpEmployment* e define a *atribuiçãoClass* para "GA". Também cria uma nova entidade *utilizadora.* Assim, o utilizador tem agora:
* Uma entidade *utilizadora empEmployment*  +  *User* que corresponde à atribuição de casa com *atribuiçãoClass* definido para "ST" e 
* Outra entidade *utilizadora empEmployment*  +  *User* que corresponde à atribuição global com *atribuiçãoClass* definido para "GA"

Para obter atributos pertencentes à atribuição padrão e ao perfil de utilizador de atribuição global, utilize os passos listados abaixo: 

* Abra a lâmina de mapeamento de atributos da sua app de provisionamento SuccessFactors. 
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique no link **Reveja o seu esquema aqui** para abrir o editor de esquemas.   
* Clique no link **Descarregar** para guardar uma cópia do esquema antes de editar.   
* No editor de esquemas, prima a tecla Ctrl-H para abrir o controlo de substituição de encontrar.
* Na caixa de texto encontrar, copiar e colar o valor`$.employmentNav.results[0]`
* Na caixa de texto substitua, copie e cole o valor `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Clique na opção "substituir todos" para atualizar o esquema. 
* Salve o esquema. 
* O processo acima atualiza todas as expressões JSONPath da seguinte forma: 
  * Velho JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Novo JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Recarregue a lâmina de mapeamento do atributo da aplicação. 
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique na **lista de atributos Editar para SuccessFactors**.
* Adicione novos atributos para obter dados de atribuição global. Por exemplo: se quiser obter o nome do departamento associado a um perfil de atribuição global, pode adicionar o atributo **globalPartamento de Assinaturas** com a expressão JSONPath definida para `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* Agora pode fluir ambos os valores do departamento para atributos ative directory ou fluir seletivamente um valor usando o mapeamento de expressão. Exemplo: a expressão abaixo define o valor do atributo do *departamento* de AD ao *globalAssignmentDepartment* se estiver presente, caso contrário, define o valor para o *departamento* associado à atribuição padrão. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Guarde o mapeamento. 
* Reinicie o provisionamento. 

### <a name="handling-concurrent-jobs-scenario"></a>Lidar com cenário de emprego simultâneo

Quando um utilizador na Central de Empregados tem empregos simultâneos/múltiplos, existem duas entidades *empEmployment* e *Utilizador* com *atribuiçãoClass* definida para "ST". Para obter atributos pertencentes a ambos os trabalhos, utilize os passos listados abaixo: 

* Abra a lâmina de mapeamento de atributos da sua app de provisionamento SuccessFactors. 
* Desloque-se para baixo e clique **Mostrar Opções avançadas**.
* Clique na **lista de atributos Editar para SuccessFactors**.
* Digamos que quer retirar o departamento associado ao trabalho 1 e ao emprego 2. O *departamento* de atributos pré-definido já adquire o valor do departamento para o primeiro emprego. Pode definir um novo atributo chamado *secondJobDepartment* e definir a expressão JSONPath para`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* Agora pode fluir ambos os valores do departamento para atributos ative directory ou fluir seletivamente um valor usando o mapeamento de expressão. 
* Guarde o mapeamento. 
* Reinicie o provisionamento. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba como configurar o SuccessFactors para o provisionamento do Ative Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Saiba como configurar a reversão para o SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Saiba mais sobre atributos de Sucessos suportados para o provisionamento de entrada](sap-successfactors-attribute-reference.md)










