---
title: SAP SuccessFactors atribuem referência
description: Saiba quais atributos de SuccessFactors são suportados pelo Fornecimento orientado pelo SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: ef2da377c7720cfb7b431d1ce0fed56656a2b8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87808512"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors atribuem referência

Neste artigo, encontrará informações sobre:

- [Entidades e atributos SuccessFactors](#supported-successfactors-entities-and-attributes)
- [Mapeamento de atributo padrão](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Apoio ao Sucesso Entidades e atributos

A tabela abaixo captura a lista de atributos SuccessFactors incluídos por padrão nas duas seguintes aplicações de provisionamento:

- [SucessoFactors para Fornecimento de Utilizadores de Diretório Ativo](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SucessoFactors para Azure AD User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Consulte a referência de [integração SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) para estender o esquema para atributos adicionais. 

| \# | Entidade de SucessoFactors                  | Atributo SuccessFactors     | Tipo de operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Ler           |
| 2  | PerPerson                              | personId                     | Ler           |
| 3  | PerPerson                              | perPersonUid                | Ler           |
| 4  | PerPersonal                            | displayName                  | Ler           |
| 5  | PerPersonal                            | nomePróprio                    | Ler           |
| 6  | PerPersonal                            | género                       | Ler           |
| 7  | PerPersonal                            | apelido                     | Ler           |
| 8  | PerPersonal                            | nome médio                   | Ler           |
| 9  | PerPersonal                            | nome preferido                | Ler           |
| 10 | Utilizador                                   | endereçoLine1                 | Ler           |
| 11 | Utilizador                                   | endereçoLine2                 | Ler           |
| 12 | Utilizador                                   | endereçoLIne3                 | Ler           |
| 13 | Utilizador                                   | businessPhone                | Ler           |
| 14 | Utilizador                                   | telemóvelPhone                    | Ler           |
| 15 | Utilizador                                   | city                         | Ler           |
| 16 | Utilizador                                   | país                      | Ler           |
| 17 | Utilizador                                   | custom01                     | Ler           |
| 18 | Utilizador                                   | personalizado02                     | Ler           |
| 19 | Utilizador                                   | personalizado03                     | Ler           |
| 20 | Utilizador                                   | custom04                     | Ler           |
| 21 | Utilizador                                   | personalizado05                     | Ler           |
| 22 | Utilizador                                   | custom06                     | Ler           |
| 23 | Utilizador                                   | custom07                     | Ler           |
| 24 | Utilizador                                   | personalizado08                     | Ler           |
| 25 | Utilizador                                   | custom09                     | Ler           |
| 26 | Utilizador                                   | personalizado10                     | Ler           |
| 27 | Utilizador                                   | custom11                     | Ler           |
| 28 | Utilizador                                   | custom12                     | Ler           |
| 29 | Utilizador                                   | custom13                     | Ler           |
| 30 | Utilizador                                   | custom14                     | Ler           |
| 31 | Utilizador                                   | empId                        | Ler           |
| 32 | Utilizador                                   | homePhone                    | Ler           |
| 33 | Utilizador                                   | trabalhoFímil                    | Ler           |
| 34 | Utilizador                                   | apelido                     | Ler           |
| 35 | Utilizador                                   | state                        | Ler           |
| 36 | Utilizador                                   | timeZone                     | Ler           |
| 37 | Utilizador                                   | nome de utilizador                     | Ler           |
| 38 | Utilizador                                   | zipCode                      | Ler           |
| 39 | PerPhone                               | areaCode                     | Ler           |
| 40 | PerPhone                               | paísDesco                  | Ler           |
| 41 | PerPhone                               | extensão                    | Ler           |
| 42 | PerPhone                               | número de telefone                  | Ler           |
| 43 | PerPhone                               | telefoneType                    | Ler           |
| 44 | Peremail                               | e-mailAddress                 | Ler, Escrever    |
| 45 | Peremail                               | e-mailType                    | Ler           |
| 46 | EmpEmployment                          | firstDateWorked              | Ler           |
| 47 | EmpEmployment                          | lastDateWorked               | Ler           |
| 48 | EmpEmployment                          | userId                       | Ler           |
| 49 | EmpEmployment                          | isContingentWorker           | Ler           |
| 50 | EmpJob                                 | paísOfCompany             | Ler           |
| 51 | EmpJob                                 | emplStatus                   | Ler           |
| 52 | EmpJob                                 | endDate                      | Ler           |
| 53 | EmpJob                                 | startDate                    | Ler           |
| 54 | EmpJob                                 | jobTitle                     | Ler           |
| 55 | EmpJob                                 | position                     | Ler           |
| 65 | EmpJob                                 | customString13               | Ler           |
| 56 | EmpJob                                 | managerId                    | Ler           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Ler           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Ler           |
| 59 | Empresa EmpJob \.                        | empresa                      | Ler           |
| 60 | Empresa EmpJob \.                        | companyId                    | Ler           |
| 61 | EmpJob \. Company \. CountryOfRegistration | duasCharCountryCode           | Ler           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Ler           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Ler           |
| 64 | EmpJob \. CostCenter                     | datacção do costCenterDesscrição        | Ler           |
| 65 | Departamento de EmpJob \.                     | departamento                   | Ler           |
| 66 | Departamento de EmpJob \.                     | departmentId                 | Ler           |
| 67 | Divisão EmpJob \.                       | divisão                     | Ler           |
| 68 | Divisão EmpJob \.                       | divisionId                   | Ler           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Ler           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Ler           |
| 71 | Localização empJob \.                       | Nome de localização                 | Ler           |
| 72 | Localização empJob \.                       | officeLocationAddress        | Ler           |
| 73 | Localização empJob \.                       | officeLocationCity           | Ler           |
| 74 | Localização empJob \.                       | officeLocationCustomString4  | Ler           |
| 75 | Localização empJob \.                       | officeLocationZipCode        | Ler           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Ler           |
| 77 | EmpEmploymentTermination               | activeEmpresocount       | Ler           |
| 78 | EmpEmploymentTermination               | últimatermãoDate        | Ler           |

## <a name="default-attribute-mapping"></a>Mapeamento de atributo padrão

A tabela abaixo fornece o mapeamento de atributos padrão entre atributos SuccessFactors listados acima e atributos AD/Azure AD. Na lâmina de fornecimento de Ad AZure, pode modificar este mapeamento predefinido para incluir atributos da lista acima. 

| \# | Entidade de SucessoFactors                  | Atributo SuccessFactors | Mapeamento de atributo aD/AD padrão   | Observação de processamento                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Usado como atributo correspondente                                                                   |
| 2  | PerPerson                              | perPersonUid            | \[Não mapeado \- usado como âncora de origem\] | Durante a sincronização inicial, o Serviço de Provisionamento liga a pessoaUuid ao objeto existenteGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | ND                                                                                           |
| 4  | PerPersonal                            | nomePróprio                | nomeDado                               | ND                                                                                           |
| 5  | PerPersonal                            | apelido                 | sn                                      | ND                                                                                           |
| 6  | Utilizador                                   | endereçoLine1             | streetAddress                           | ND                                                                                           |
| 7  | Utilizador                                   | city                     | l                                       | ND                                                                                           |
| 8  | Utilizador                                   | país                  | co                                      | ND                                                                                           |
| 9  | Utilizador                                   | state                    | SC                                      | ND                                                                                           |
| 10 | Utilizador                                   | nome de utilizador                 | NomeContaSam                          | ND                                                                                           |
| 11 | Utilizador                                   | zipCode                  | código postal                              | ND                                                                                           |
| 12 | Peremail                               | e-mailAddress             | correio                                    | ND                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | título                                   | ND                                                                                           |
| 14 | EmpJob                                 | managerId                | gestor                                 | ND                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | duasCharCountryCode       | c                                       | ND                                                                                           |
| 16 | Departamento de EmpJob \.                     | departamento               | departamento                              | ND                                                                                           |
| 17 | Divisão EmpJob \.                       | divisão                 | empresa                                 | ND                                                                                           |
| 18 | Localização empJob \.                       | officeLocationAddress    | streetAddress                           | ND                                                                                           |
| 19 | Localização empJob \.                       | officeLocationZipCode    | código postal                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmpresocount   | accountEnabled                          | se activarEmpregoso=0, desative a conta\.                                           |
