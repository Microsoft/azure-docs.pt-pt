---
title: SAP SuccessFactors atribuem referência
description: Saiba quais atributos de SuccessFactors são suportados pelo Fornecimento orientado pelo SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255547"
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

| \# | Entidade de SucessoFactors                  | Atributo SuccessFactors     | Tipo de Operação |
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
| 10 | User                                   | endereçoLine1                 | Ler           |
| 11 | User                                   | endereçoLine2                 | Ler           |
| 12 | User                                   | endereçoLIne3                 | Ler           |
| 13 | User                                   | businessPhone                | Ler           |
| 14 | User                                   | telemóvelPhone                    | Ler           |
| 15 | User                                   | city                         | Ler           |
| 16 | User                                   | país                      | Ler           |
| 17 | User                                   | custom01                     | Ler           |
| 18 | User                                   | personalizado02                     | Ler           |
| 19 | User                                   | personalizado03                     | Ler           |
| 20 | User                                   | custom04                     | Ler           |
| 21 | User                                   | personalizado05                     | Ler           |
| 22 | User                                   | custom06                     | Ler           |
| 23 | User                                   | custom07                     | Ler           |
| 24 | User                                   | personalizado08                     | Ler           |
| 25 | User                                   | custom09                     | Ler           |
| 26 | User                                   | personalizado10                     | Ler           |
| 27 | User                                   | custom11                     | Ler           |
| 28 | User                                   | custom12                     | Ler           |
| 29 | User                                   | custom13                     | Ler           |
| 30 | User                                   | custom14                     | Ler           |
| 31 | User                                   | empId                        | Ler           |
| 32 | User                                   | homePhone                    | Ler           |
| 33 | User                                   | trabalhoFímil                    | Ler           |
| 34 | User                                   | apelido                     | Ler           |
| 35 | User                                   | state                        | Ler           |
| 36 | User                                   | timeZone                     | Ler           |
| 37 | User                                   | nome de utilizador                     | Ler           |
| 38 | User                                   | zipCode                      | Ler           |
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
| 6  | User                                   | endereçoLine1             | streetAddress                           | ND                                                                                           |
| 7  | User                                   | city                     | l                                       | ND                                                                                           |
| 8  | User                                   | país                  | co                                      | ND                                                                                           |
| 9  | User                                   | state                    | SC                                      | ND                                                                                           |
| 10 | User                                   | nome de utilizador                 | NomeContaSam                          | ND                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | ND                                                                                           |
| 12 | Peremail                               | e-mailAddress             | correio                                    | ND                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | título                                   | ND                                                                                           |
| 14 | EmpJob                                 | managerId                | gestor                                 | ND                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | duasCharCountryCode       | c                                       | ND                                                                                           |
| 16 | Departamento de EmpJob \.                     | departamento               | departamento                              | ND                                                                                           |
| 17 | Divisão EmpJob \.                       | divisão                 | empresa                                 | ND                                                                                           |
| 18 | Localização empJob \.                       | officeLocationAddress    | streetAddress                           | ND                                                                                           |
| 19 | Localização empJob \.                       | officeLocationZipCode    | postalCode                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmpresocount   | accountEnabled                          | se activarEmpregoso=0, desative a conta\.                                           |
