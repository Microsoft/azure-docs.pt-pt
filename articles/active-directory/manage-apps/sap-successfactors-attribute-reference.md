---
title: Referência de atributo SAP SuccessFactors | Microsoft Docs
description: Saiba quais atributos de SuccessFactors são suportados pelo provisionamento controlado por SuccessFactors-HR
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971116"
---
# <a name="sap-successfactors-attribute-reference"></a>Referência de atributo SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Atributos e entidades SuccessFactors com suporte

A tabela a seguir captura a lista de atributos SuccessFactors com suporte nos dois aplicativos de provisionamento a seguir: 
* [SuccessFactors para Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors para provisionamento de usuário do Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors     | Tipo de Operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Perpessoa                              | personIdExternal             | Leitura           |
| 2  | Perpessoa                              | personId                     | Leitura           |
| 3  | Perpessoa                              | perPersonUuid                | Leitura           |
| 4  | Entre pessoas                            | displayName                  | Leitura           |
| 5  | Entre pessoas                            | firstName                    | Leitura           |
| 6  | Entre pessoas                            | género                       | Leitura           |
| 7  | Entre pessoas                            | lastName                     | Leitura           |
| 8  | Entre pessoas                            | middleName                   | Leitura           |
| 9  | Entre pessoas                            | preferência                | Leitura           |
| 10 | Utilizador                                   | addressLine1                 | Leitura           |
| 11 | Utilizador                                   | Endereço2                 | Leitura           |
| 12 | Utilizador                                   | addressLIne3                 | Leitura           |
| 13 | Utilizador                                   | Ficha                | Leitura           |
| 14 | Utilizador                                   | Celular                    | Leitura           |
| 15 | Utilizador                                   | city                         | Leitura           |
| 16 | Utilizador                                   | país                      | Leitura           |
| 17 | Utilizador                                   | custom01                     | Leitura           |
| 18 | Utilizador                                   | custom02                     | Leitura           |
| 19 | Utilizador                                   | custom03                     | Leitura           |
| 20 | Utilizador                                   | custom04                     | Leitura           |
| 21 | Utilizador                                   | custom05                     | Leitura           |
| 22 | Utilizador                                   | custom06                     | Leitura           |
| 23 | Utilizador                                   | custom07                     | Leitura           |
| 24 | Utilizador                                   | custom08                     | Leitura           |
| 25 | Utilizador                                   | custom09                     | Leitura           |
| 26 | Utilizador                                   | personalizado10                     | Leitura           |
| 27 | Utilizador                                   | custom11                     | Leitura           |
| 28 | Utilizador                                   | custom12                     | Leitura           |
| 29 | Utilizador                                   | custom13                     | Leitura           |
| 30 | Utilizador                                   | custom14                     | Leitura           |
| 31 | Utilizador                                   | empId                        | Leitura           |
| 32 | Utilizador                                   | homePhone                    | Leitura           |
| 33 | Utilizador                                   | jobFamily                    | Leitura           |
| 34 | Utilizador                                   | apelido                     | Leitura           |
| 35 | Utilizador                                   | state                        | Leitura           |
| 36 | Utilizador                                   | timeZone                     | Leitura           |
| 37 | Utilizador                                   | o nome de utilizador                     | Leitura           |
| 38 | Utilizador                                   | zipCode                      | Leitura           |
| 39 | Por telefone                               | areaCode                     | Leitura           |
| 40 | Por telefone                               | countryCode                  | Leitura           |
| 41 | Por telefone                               | extensão                    | Leitura           |
| 42 | Por telefone                               | phoneNumber                  | Leitura           |
| 43 | Por telefone                               | telefonetype                    | Leitura           |
| 44 | Enviar por email                               | emailAddress                 | Leitura, gravação    |
| 45 | Enviar por email                               | emailtype                    | Leitura           |
| 46 | EmpEmployment                          | firstDateWorked              | Leitura           |
| 47 | EmpEmployment                          | lastDateWorked               | Leitura           |
| 48 | EmpEmployment                          | userId                       | Leitura           |
| 49 | EmpEmployment                          | isContingentWorker           | Leitura           |
| 50 | EmpJob                                 | countryOfCompany             | Leitura           |
| 51 | EmpJob                                 | emplStatus                   | Leitura           |
| 52 | EmpJob                                 | Término                      | Leitura           |
| 53 | EmpJob                                 | Início                    | Leitura           |
| 54 | EmpJob                                 | jobTitle                     | Leitura           |
| 55 | EmpJob                                 | posição                     | Leitura           |
| 65 | EmpJob                                 | customString13               | Leitura           |
| 56 | EmpJob                                 | managerId                    | Leitura           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Leitura           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Leitura           |
| 59 | EmpJob\.empresa                        | Empresa                      | Leitura           |
| 60 | EmpJob\.empresa                        | companyId                    | Leitura           |
| 61 | EmpJob\.empresa\.CountryOfRegistration | twoCharCountryCode           | Leitura           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Leitura           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Leitura           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Leitura           |
| 65 | Departamento de\.de EmpJob                     | Departamento                   | Leitura           |
| 66 | Departamento de\.de EmpJob                     | departmentId                 | Leitura           |
| 67 | Divisão de\.de EmpJob                       | Divisão                     | Leitura           |
| 68 | Divisão de\.de EmpJob                       | divisionId                   | Leitura           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Leitura           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Leitura           |
| 71 | EmpJob\.local                       | LocalName                 | Leitura           |
| 72 | EmpJob\.local                       | officeLocationAddress        | Leitura           |
| 73 | EmpJob\.local                       | officeLocationCity           | Leitura           |
| 74 | EmpJob\.local                       | officeLocationCustomString4  | Leitura           |
| 75 | EmpJob\.local                       | officeLocationZipCode        | Leitura           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Leitura           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Leitura           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Leitura           |


## <a name="default-attribute-mapping"></a>Mapeamento de atributo padrão

A tabela a seguir fornece o mapeamento de atributo padrão entre os atributos SuccessFactors listados acima e os atributos AD/Azure AD. Na folha "mapeamento" do aplicativo de provisionamento do Azure AD, você pode modificar esse mapeamento padrão para incluir atributos da lista acima. 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors | Mapeamento de atributo padrão AD/AD do Azure   | Comentário de processamento                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Perpessoa                              | personIdExternal         | employeeId                              | Usado como atributo correspondente                                                                   |
| 2  | Perpessoa                              | perPersonUuid            | \[não mapeado \- usado como âncora de origem\] | Durante a sincronização inicial, o serviço de provisionamento vincula o personUuid a objectGuid\. existentes  |
| 3  | Entre pessoas                            | displayName              | displayName                             | N/D                                                                                           |
| 4  | Entre pessoas                            | firstName                | givenName                               | N/D                                                                                           |
| 5  | Entre pessoas                            | lastName                 | sn                                      | N/D                                                                                           |
| 6  | Utilizador                                   | addressLine1             | streetAddress                           | N/D                                                                                           |
| 7  | Utilizador                                   | city                     | l                                       | N/D                                                                                           |
| 8  | Utilizador                                   | país                  | Co                                      | N/D                                                                                           |
| 9  | Utilizador                                   | state                    | St                                      | N/D                                                                                           |
| 10 | Utilizador                                   | o nome de utilizador                 | samAccountName                          | N/D                                                                                           |
| 11 | Utilizador                                   | zipCode                  | postalCode                              | N/D                                                                                           |
| 12 | Enviar por email                               | emailAddress             | correio                                    | N/D                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | N/D                                                                                           |
| 14 | EmpJob                                 | managerId                | gestor                                 | N/D                                                                                           |
| 15 | EmpJob\.empresa\.CountryOfRegistration | twoCharCountryCode       | c                                       | N/D                                                                                           |
| 16 | Departamento de\.de EmpJob                     | Departamento               | Departamento                              | N/D                                                                                           |
| 17 | Divisão de\.de EmpJob                       | Divisão                 | Empresa                                 | N/D                                                                                           |
| 18 | EmpJob\.local                       | officeLocationAddress    | streetAddress                           | N/D                                                                                           |
| 19 | EmpJob\.local                       | officeLocationZipCode    | postalCode                              | N/D                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | se activeEmploymentsCount = 0, desabilite o account\.                                           |

