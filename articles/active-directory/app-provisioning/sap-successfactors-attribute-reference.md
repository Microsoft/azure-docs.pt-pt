---
title: SAP SuccessFactors atribuem referência
description: Saiba quais atributos de SuccessFactors são suportados pelo Fornecimento orientado pelo SuccessFactors-HR
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 12/06/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 25541b76dda55db1ec26f4d8e3ec63573a47e7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781553"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors atribuem referência

Neste artigo, encontrará informações sobre:

- [Apoio ao Sucesso Entidades e atributos](#supported-successfactors-entities-and-attributes)
- [Mapeamento de atributo padrão](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Apoio ao Sucesso Entidades e atributos

A tabela abaixo captura a lista de atributos SuccessFactors suportados pelas seguintes duas aplicações de provisionamento:

- [SucessoFactors para Fornecimento de Utilizadores de Diretório Ativo](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SucessoFactors para Azure AD User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)


| \# | Entidade de SucessoFactors                  | Atributo SuccessFactors     | Tipo de operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Leitura           |
| 2  | PerPerson                              | personId                     | Leitura           |
| 3  | PerPerson                              | perPersonUid                | Leitura           |
| 4  | PerPersonal                            | displayName                  | Leitura           |
| 5  | PerPersonal                            | nomePróprio                    | Leitura           |
| 6  | PerPersonal                            | género                       | Leitura           |
| 7  | PerPersonal                            | apelido                     | Leitura           |
| 8  | PerPersonal                            | nome médio                   | Leitura           |
| 9  | PerPersonal                            | nome preferido                | Leitura           |
| 10 | Utilizador                                   | endereçoLine1                 | Leitura           |
| 11 | Utilizador                                   | endereçoLine2                 | Leitura           |
| 12 | Utilizador                                   | endereçoLIne3                 | Leitura           |
| 13 | Utilizador                                   | businessPhone                | Leitura           |
| 14 | Utilizador                                   | telemóvelPhone                    | Leitura           |
| 15 | Utilizador                                   | city                         | Leitura           |
| 16 | Utilizador                                   | país                      | Leitura           |
| 17 | Utilizador                                   | custom01                     | Leitura           |
| 18 | Utilizador                                   | personalizado02                     | Leitura           |
| 19 | Utilizador                                   | personalizado03                     | Leitura           |
| 20 | Utilizador                                   | custom04                     | Leitura           |
| 21 | Utilizador                                   | personalizado05                     | Leitura           |
| 22 | Utilizador                                   | custom06                     | Leitura           |
| 23 | Utilizador                                   | custom07                     | Leitura           |
| 24 | Utilizador                                   | personalizado08                     | Leitura           |
| 25 | Utilizador                                   | custom09                     | Leitura           |
| 26 | Utilizador                                   | personalizado10                     | Leitura           |
| 27 | Utilizador                                   | custom11                     | Leitura           |
| 28 | Utilizador                                   | custom12                     | Leitura           |
| 29 | Utilizador                                   | custom13                     | Leitura           |
| 30 | Utilizador                                   | custom14                     | Leitura           |
| 31 | Utilizador                                   | empId                        | Leitura           |
| 32 | Utilizador                                   | homePhone                    | Leitura           |
| 33 | Utilizador                                   | trabalhoFímil                    | Leitura           |
| 34 | Utilizador                                   | apelido                     | Leitura           |
| 35 | Utilizador                                   | state                        | Leitura           |
| 36 | Utilizador                                   | timeZone                     | Leitura           |
| 37 | Utilizador                                   | o nome de utilizador                     | Leitura           |
| 38 | Utilizador                                   | zipCode                      | Leitura           |
| 39 | PerPhone                               | areaCode                     | Leitura           |
| 40 | PerPhone                               | paísDesco                  | Leitura           |
| 41 | PerPhone                               | extensão                    | Leitura           |
| 42 | PerPhone                               | número de telefone                  | Leitura           |
| 43 | PerPhone                               | telefoneType                    | Leitura           |
| 44 | Peremail                               | e-mailAddress                 | Ler, Escrever    |
| 45 | Peremail                               | e-mailType                    | Leitura           |
| 46 | EmpEmployment                          | firstDateWorked              | Leitura           |
| 47 | EmpEmployment                          | lastDateWorked               | Leitura           |
| 48 | EmpEmployment                          | userId                       | Leitura           |
| 49 | EmpEmployment                          | isContingentWorker           | Leitura           |
| 50 | EmpJob                                 | paísOfCompany             | Leitura           |
| 51 | EmpJob                                 | emplStatus                   | Leitura           |
| 52 | EmpJob                                 | endDate                      | Leitura           |
| 53 | EmpJob                                 | startDate                    | Leitura           |
| 54 | EmpJob                                 | jobTitle                     | Leitura           |
| 55 | EmpJob                                 | position                     | Leitura           |
| 65 | EmpJob                                 | customString13               | Leitura           |
| 56 | EmpJob                                 | managerId                    | Leitura           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Leitura           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Leitura           |
| 59 | Empresa EmpJob \.                        | empresa                      | Leitura           |
| 60 | Empresa EmpJob \.                        | companyId                    | Leitura           |
| 61 | EmpJob \. Company \. CountryOfRegistration | duasCharCountryCode           | Leitura           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Leitura           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Leitura           |
| 64 | EmpJob \. CostCenter                     | datacção do costCenterDesscrição        | Leitura           |
| 65 | Departamento de EmpJob \.                     | departamento                   | Leitura           |
| 66 | Departamento de EmpJob \.                     | departmentId                 | Leitura           |
| 67 | Divisão EmpJob \.                       | divisão                     | Leitura           |
| 68 | Divisão EmpJob \.                       | divisionId                   | Leitura           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Leitura           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Leitura           |
| 71 | Localização empJob \.                       | Nome de localização                 | Leitura           |
| 72 | Localização empJob \.                       | officeLocationAddress        | Leitura           |
| 73 | Localização empJob \.                       | officeLocationCity           | Leitura           |
| 74 | Localização empJob \.                       | officeLocationCustomString4  | Leitura           |
| 75 | Localização empJob \.                       | officeLocationZipCode        | Leitura           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Leitura           |
| 77 | EmpEmploymentTermination               | activeEmpresocount       | Leitura           |
| 78 | EmpEmploymentTermination               | últimatermãoDate        | Leitura           |

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
| 10 | Utilizador                                   | o nome de utilizador                 | NomeContaSam                          | ND                                                                                           |
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
