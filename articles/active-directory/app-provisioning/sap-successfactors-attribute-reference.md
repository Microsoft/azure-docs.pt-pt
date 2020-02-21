---
title: SAP SuccessFactors Atribuição Referência  Microsoft Docs
description: Saiba quais os atributos de SuccessFactors suportados por fornecimento sonante SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522361"
---
# <a name="sap-successfactors-attribute-reference"></a>Referência de atributos de sucesso sAP

## <a name="supported-successfactors-entities-and-attributes"></a>Entidades e Atributos de Sucesso Suportados

A tabela abaixo capta a lista de atributos SuccessFactors suportados pelas seguintes duas aplicações de provisionamento: 
* [SuccessFactors para fornecimento de utilizadores de diretório ativo](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors para fornecimento de utilizadores de Anúncios Azure](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entidade SuccessFactors                  | Atributo saque de SuccessFactors     | Tipo de Operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Leitura           |
| 2  | PerPerson                              | personId                     | Leitura           |
| 3  | PerPerson                              | perPersonUuid                | Leitura           |
| 4  | PerPersonal                            | displayName                  | Leitura           |
| 5  | PerPersonal                            | firstName                    | Leitura           |
| 6  | PerPersonal                            | género                       | Leitura           |
| 7  | PerPersonal                            | lastName                     | Leitura           |
| 8  | PerPersonal                            | middleName                   | Leitura           |
| 9  | PerPersonal                            | nome preferido                | Leitura           |
| 10 | Utilizador                                   | endereçoLine1                 | Leitura           |
| 11 | Utilizador                                   | addressLine2                 | Leitura           |
| 12 | Utilizador                                   | endereçoLIne3                 | Leitura           |
| 13 | Utilizador                                   | businessPhone                | Leitura           |
| 14 | Utilizador                                   | telemóvelPhone                    | Leitura           |
| 15 | Utilizador                                   | city                         | Leitura           |
| 16 | Utilizador                                   | país                      | Leitura           |
| 17 | Utilizador                                   | personalizado01                     | Leitura           |
| 18 | Utilizador                                   | personalizado02                     | Leitura           |
| 19 | Utilizador                                   | personalizado03                     | Leitura           |
| 20 | Utilizador                                   | personalizado04                     | Leitura           |
| 21 | Utilizador                                   | personalizado05                     | Leitura           |
| 22 | Utilizador                                   | personalizado06                     | Leitura           |
| 23 | Utilizador                                   | personalizado07                     | Leitura           |
| 24 | Utilizador                                   | personalizado08                     | Leitura           |
| 25 | Utilizador                                   | personalizado09                     | Leitura           |
| 26 | Utilizador                                   | personalizado10                     | Leitura           |
| 27 | Utilizador                                   | personalizado11                     | Leitura           |
| 28 | Utilizador                                   | personalizado12                     | Leitura           |
| 29 | Utilizador                                   | personalizado13                     | Leitura           |
| 30 | Utilizador                                   | personalizado14                     | Leitura           |
| 31 | Utilizador                                   | empId                        | Leitura           |
| 32 | Utilizador                                   | homePhone                    | Leitura           |
| 33 | Utilizador                                   | empregoFamília                    | Leitura           |
| 34 | Utilizador                                   | apelido                     | Leitura           |
| 35 | Utilizador                                   | state                        | Leitura           |
| 36 | Utilizador                                   | timeZone                     | Leitura           |
| 37 | Utilizador                                   | o nome de utilizador                     | Leitura           |
| 38 | Utilizador                                   | zipCode                      | Leitura           |
| 39 | PerPhone                               | áreaCódigo                     | Leitura           |
| 40 | PerPhone                               | countryCode                  | Leitura           |
| 41 | PerPhone                               | extensão                    | Leitura           |
| 42 | PerPhone                               | phoneNumber                  | Leitura           |
| 43 | PerPhone                               | telefoneTipo                    | Leitura           |
| 44 | PerEmail                               | emailAddress                 | Ler, Escrever    |
| 45 | PerEmail                               | e-mailType                    | Leitura           |
| 46 | EmpEmployment                          | firstDateWorked              | Leitura           |
| 47 | EmpEmployment                          | últimoDataTrabalhado               | Leitura           |
| 48 | EmpEmployment                          | userId                       | Leitura           |
| 49 | EmpEmployment                          | éContingentWorker           | Leitura           |
| 50 | EmpJob                                 | countryOfCompany             | Leitura           |
| 51 | EmpJob                                 | emplStatus                   | Leitura           |
| 52 | EmpJob                                 | data de fim                      | Leitura           |
| 53 | EmpJob                                 | inícioData                    | Leitura           |
| 54 | EmpJob                                 | jobTitle                     | Leitura           |
| 55 | EmpJob                                 | Posição                     | Leitura           |
| 65 | EmpJob                                 | customString13               | Leitura           |
| 56 | EmpJob                                 | managerId                    | Leitura           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Leitura           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Leitura           |
| 59 | Empresa de\.EmpJob                        | Empresa                      | Leitura           |
| 60 | Empresa de\.EmpJob                        | companyId                    | Leitura           |
| 61 | Empresa de\.EmpJob\.CountryOfRegistration | doisCharCountryCode           | Leitura           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Leitura           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Leitura           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Leitura           |
| 65 | Departamento de\.emPJob                     | Departamento                   | Leitura           |
| 66 | Departamento de\.emPJob                     | departamentoId                 | Leitura           |
| 67 | Divisão de\.EmpJob                       | divisão                     | Leitura           |
| 68 | Divisão de\.EmpJob                       | divisãoId                   | Leitura           |
| 69 | EmpJob\.JobCode                        | empregoCódigo                      | Leitura           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Leitura           |
| 71 | Localização\.EmpJob                       | Nome de localização                 | Leitura           |
| 72 | Localização\.EmpJob                       | officeLocationAddress        | Leitura           |
| 73 | Localização\.EmpJob                       | officeLocationCity           | Leitura           |
| 74 | Localização\.EmpJob                       | officeLocationCustomString4  | Leitura           |
| 75 | Localização\.EmpJob                       | officeLocationZipCode        | Leitura           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Leitura           |
| 77 | EmpEmploymentTermination               | activoEmpregosCount       | Leitura           |
| 78 | EmpEmploymentTermination               | data de rescisão mais recente        | Leitura           |


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

