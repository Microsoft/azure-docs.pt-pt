---
title: SAP SuccessFactors Atribuição Referência  Microsoft Docs
description: Saiba quais os atributos de SuccessFactors suportados por fornecimento sonante SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066190"
---
# <a name="sap-successfactors-attribute-reference"></a>Referência de atributos de sucesso sAP

## <a name="supported-successfactors-entities-and-attributes"></a>Entidades e Atributos de Sucesso Suportados

A tabela abaixo capta a lista de atributos SuccessFactors suportados pelas seguintes duas aplicações de provisionamento: 
* [SuccessFactors para fornecimento de utilizadores de diretório ativo](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors para fornecimento de utilizadores de Anúncios Azure](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entidade SuccessFactors                  | Atributo saque de SuccessFactors     | Tipo de Operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Ler           |
| 2  | PerPerson                              | personId                     | Ler           |
| 3  | PerPerson                              | perPersonUuid                | Ler           |
| 4  | PerPersonal                            | displayName                  | Ler           |
| 5  | PerPersonal                            | firstName                    | Ler           |
| 6  | PerPersonal                            | género                       | Ler           |
| 7  | PerPersonal                            | lastName                     | Ler           |
| 8  | PerPersonal                            | middleName                   | Ler           |
| 9  | PerPersonal                            | nome preferido                | Ler           |
| 10 | Utilizador                                   | endereçoLine1                 | Ler           |
| 11 | Utilizador                                   | addressLine2                 | Ler           |
| 12 | Utilizador                                   | endereçoLIne3                 | Ler           |
| 13 | Utilizador                                   | businessPhone                | Ler           |
| 14 | Utilizador                                   | telemóvelPhone                    | Ler           |
| 15 | Utilizador                                   | city                         | Ler           |
| 16 | Utilizador                                   | país                      | Ler           |
| 17 | Utilizador                                   | personalizado01                     | Ler           |
| 18 | Utilizador                                   | personalizado02                     | Ler           |
| 19 | Utilizador                                   | personalizado03                     | Ler           |
| 20 | Utilizador                                   | personalizado04                     | Ler           |
| 21 | Utilizador                                   | personalizado05                     | Ler           |
| 22 | Utilizador                                   | personalizado06                     | Ler           |
| 23 | Utilizador                                   | personalizado07                     | Ler           |
| 24 | Utilizador                                   | personalizado08                     | Ler           |
| 25 | Utilizador                                   | personalizado09                     | Ler           |
| 26 | Utilizador                                   | personalizado10                     | Ler           |
| 27 | Utilizador                                   | personalizado11                     | Ler           |
| 28 | Utilizador                                   | personalizado12                     | Ler           |
| 29 | Utilizador                                   | personalizado13                     | Ler           |
| 30 | Utilizador                                   | personalizado14                     | Ler           |
| 31 | Utilizador                                   | empId                        | Ler           |
| 32 | Utilizador                                   | homePhone                    | Ler           |
| 33 | Utilizador                                   | empregoFamília                    | Ler           |
| 34 | Utilizador                                   | apelido                     | Ler           |
| 35 | Utilizador                                   | state                        | Ler           |
| 36 | Utilizador                                   | timeZone                     | Ler           |
| 37 | Utilizador                                   | o nome de utilizador                     | Ler           |
| 38 | Utilizador                                   | zipCode                      | Ler           |
| 39 | PerPhone                               | áreaCódigo                     | Ler           |
| 40 | PerPhone                               | countryCode                  | Ler           |
| 41 | PerPhone                               | extensão                    | Ler           |
| 42 | PerPhone                               | phoneNumber                  | Ler           |
| 43 | PerPhone                               | telefoneTipo                    | Ler           |
| 44 | PerEmail                               | emailAddress                 | Ler, Escrever    |
| 45 | PerEmail                               | e-mailType                    | Ler           |
| 46 | EmpEmployment                          | firstDateWorked              | Ler           |
| 47 | EmpEmployment                          | últimoDataTrabalhado               | Ler           |
| 48 | EmpEmployment                          | userId                       | Ler           |
| 49 | EmpEmployment                          | éContingentWorker           | Ler           |
| 50 | EmpJob                                 | countryOfCompany             | Ler           |
| 51 | EmpJob                                 | emplStatus                   | Ler           |
| 52 | EmpJob                                 | data de fim                      | Ler           |
| 53 | EmpJob                                 | inícioData                    | Ler           |
| 54 | EmpJob                                 | jobTitle                     | Ler           |
| 55 | EmpJob                                 | Posição                     | Ler           |
| 65 | EmpJob                                 | customString13               | Ler           |
| 56 | EmpJob                                 | managerId                    | Ler           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Ler           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Ler           |
| 59 | Empresa de\.EmpJob                        | Empresa                      | Ler           |
| 60 | Empresa de\.EmpJob                        | companyId                    | Ler           |
| 61 | Empresa de\.EmpJob\.CountryOfRegistration | doisCharCountryCode           | Ler           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Ler           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Ler           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Ler           |
| 65 | Departamento de\.emPJob                     | Departamento                   | Ler           |
| 66 | Departamento de\.emPJob                     | departamentoId                 | Ler           |
| 67 | Divisão de\.EmpJob                       | divisão                     | Ler           |
| 68 | Divisão de\.EmpJob                       | divisãoId                   | Ler           |
| 69 | EmpJob\.JobCode                        | empregoCódigo                      | Ler           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Ler           |
| 71 | Localização\.EmpJob                       | Nome de localização                 | Ler           |
| 72 | Localização\.EmpJob                       | officeLocationAddress        | Ler           |
| 73 | Localização\.EmpJob                       | officeLocationCity           | Ler           |
| 74 | Localização\.EmpJob                       | officeLocationCustomString4  | Ler           |
| 75 | Localização\.EmpJob                       | officeLocationZipCode        | Ler           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Ler           |
| 77 | EmpEmploymentTermination               | activoEmpregosCount       | Ler           |
| 78 | EmpEmploymentTermination               | data de rescisão mais recente        | Ler           |


## <a name="default-attribute-mapping"></a>Mapeamento de atributo padrão

A tabela abaixo fornece o mapeamento padrão de atributos entre os atributos SuccessFactors listados acima e atributos AD/Azure AD. Na lâmina de provisionamento da AD Azure "Mapping", pode modificar este mapeamento padrão para incluir atributos da lista acima. 

| \# | Entidade SuccessFactors                  | Atributo saque de SuccessFactors | Mapeamento de atributo ad/azure predefinido   | Observação de processamento                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Usado como atributo correspondente                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[não mapeado \- usado como âncora de origem\] | Durante a sincronização inicial, o Serviço de Provisionamento liga a pessoaUuid ao objectuide existente.  |
| 3  | PerPersonal                            | displayName              | displayName                             | ND                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | ND                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | ND                                                                                           |
| 6  | Utilizador                                   | endereçoLine1             | streetAddress                           | ND                                                                                           |
| 7  | Utilizador                                   | city                     | l                                       | ND                                                                                           |
| 8  | Utilizador                                   | país                  | Co                                      | ND                                                                                           |
| 9  | Utilizador                                   | state                    | St                                      | ND                                                                                           |
| 10 | Utilizador                                   | o nome de utilizador                 | samAccountName                          | ND                                                                                           |
| 11 | Utilizador                                   | zipCode                  | postalCode                              | ND                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | ND                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | Título                                   | ND                                                                                           |
| 14 | EmpJob                                 | managerId                | Manager                                 | ND                                                                                           |
| 15 | Empresa de\.EmpJob\.CountryOfRegistration | doisCharCountryCode       | c                                       | ND                                                                                           |
| 16 | Departamento de\.emPJob                     | Departamento               | Departamento                              | ND                                                                                           |
| 17 | Divisão de\.EmpJob                       | divisão                 | Empresa                                 | ND                                                                                           |
| 18 | Localização\.EmpJob                       | officeLocationAddress    | streetAddress                           | ND                                                                                           |
| 19 | Localização\.EmpJob                       | officeLocationZipCode    | postalCode                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activoEmpregosCount   | accountEnabled                          | se activoEmpregosCount=0, desative a conta\.                                           |

