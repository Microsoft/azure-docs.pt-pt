---
title: Módulo Ansible e matriz de versão para o Azure
description: Módulo Ansible e matriz de versão para o Azure
keywords: Ansible, funções, matriz, versão, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241546"
---
# <a name="ansible-module-and-version-matrix"></a>Módulo Ansible e matriz de versão

O Ansible inclui um conjunto de módulos para uso no provisionamento e configuração de recursos do Azure. Esses recursos incluem máquinas virtuais, conjuntos de dimensionamento, serviços de rede e serviços de contêiner. Este artigo lista os vários módulos do Ansible para o Azure e as versões do Ansible em que eles são fornecidos.

## <a name="ansible-modules-for-azure"></a>Módulos Ansible para o Azure

Os módulos a seguir podem ser executados diretamente em hosts remotos ou por meio de guias estratégicos.

Esses módulos estão disponíveis na versão oficial do Ansible e nas seguintes funções do guia estratégico da Microsoft.

| Módulo Ansible para o Azure                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Função Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Computação**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_availabilityset_facts              | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_deployment                         | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_functionapp                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_functionapp_facts                  | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_image                              | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_resource                           | -            | -                           | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resource_facts                     | -            | -                           | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resourcegroup                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_resourcegroup_facts                | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachine                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_virtualmachineextension           | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachineimage_facts          | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescaleset            | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescaleset_facts      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Sim          | Sim          |
| **Redes**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Sim          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Sim          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Sim          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Sim          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Sim          | Sim          |
| azure_rm_dnsrecordset                       | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnsrecordset_facts                 | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnszone                            | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_dnszone_facts                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_loadbalancer                       | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_loadbalancer_facts                 | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_networkinterface                   | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_networkinterface_facts             | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_publicipaddress                    | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_publicipaddress_facts              | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_route                              | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_routetable                         | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_securitygroup                      | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_subnet                             | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetwork                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetwork_facts               | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Sim          | Sim          |
| **Armazenamento**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_manageddisk_facts                  | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageaccount                     | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageaccount_facts               | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_storageblob                        | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_webapp                             | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Sim          | Sim          |
| **Contentores**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Sim          | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aks                                | -            | -                           | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aks_facts                          | -            | -                           | Sim          | Sim          | Sim          | Sim          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerinstance                  | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistry                  | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| **Bases de dados**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqldatabase                      | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_mysqlserver                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqldatabase                 | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_postgresqlserver                   | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqldatabase                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Sim          | Sim          | Sim          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_sqlserver                          | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_sqlserver_facts                    | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| **Análise**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Sim          | Sim          |
| **Integrar**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Sim          | Sim          |
| **Segurança**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Sim          | Sim          |
| azure_rm_keyvaultkey                        | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_keyvaultsecret                     | -            | Sim                         | Sim          | Sim          | Sim          | Sim          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Sim          | Sim          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Sim          | Sim          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Sim          | Sim          | Sim          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Sim          | Sim          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Sim          | Sim          |

## <a name="introduction-to-playbook-role-for-azure"></a>Introdução à função de guia estratégico do Azure

A [função de guia estratégico de azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) inclui todos os módulos mais recentes do Azure. As atualizações e correções de bugs são realizadas de maneira mais oportuna do que a versão oficial do Ansible. Se você usar o Ansible para fins de provisionamento de recursos do Azure, você será incentivado a instalar a função de guia estratégico `azure_preview_module`.

A função de guia estratégico `azure_preview_module` é lançada a cada três semanas.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as funções de guia estratégico, consulte [criando guias estratégicos reutilizáveis](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
