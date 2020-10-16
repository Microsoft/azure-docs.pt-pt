---
title: Controlos de segurança para o Serviço de Aplicações Azure
description: Encontre uma lista de controlos de segurança para avaliar o Serviço de Aplicações Azure para a sua organização.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962932"
---
# <a name="security-controls-for-azure-app-service"></a>Controlos de segurança para o Serviço de Aplicações Azure

Este artigo documenta os controlos de segurança incorporados no Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte ao ponto final de serviço| Sim | Disponível para Serviço de Aplicações.| [Restrições de acesso ao serviço de aplicações Azure](app-service-ip-restrictions.md)
| Suporte à injeção VNet| Sim | Os Ambientes de Serviço de Aplicações são implementações privadas do Serviço de Aplicações dedicadas a um único cliente injetado na rede virtual de um cliente. | [Introdução aos Ambientes de Serviço de Aplicações](environment/intro.md)
| Suporte de isolamento e firewalling de rede| Sim | Para a variação pública multi-inquilino do Serviço de Aplicações, os clientes podem configurar ACLs de rede (Restrições IP) para bloquear o tráfego de entrada permitido.  Os Ambientes de Serviço de Aplicações são implantados diretamente em redes virtuais e, portanto, podem ser protegidos com NSGs. | [Restrições de acesso ao serviço de aplicações Azure](app-service-ip-restrictions.md)
| Suporte de túneis forçados| Sim | Os Ambientes de Serviço de Aplicações podem ser implantados na rede virtual de um cliente onde os túneis forçados são configurados. | [Configurar o Ambiente de Serviço de Aplicações com túnel forçado](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | O Serviço de Aplicações integra-se com Insights de Aplicação para idiomas que suportam Insights de Aplicação (Full.NET Framework, .NET Core, Java e Node.JS).  Consulte [o desempenho do Serviço de Aplicações Monitor Azure](../azure-monitor/app/azure-web-apps.md). O Serviço de Aplicações também envia métricas de aplicação para o Azure Monitor. | [Monitorize aplicativos no Azure App Service](web-sites-monitor.md)
| Registo e auditoria de avião de controlo e gestão| Sim | Todas as operações de gestão realizadas em objetos do Serviço de Aplicações ocorrem através [do Azure Resource Manager.](../azure-resource-manager/index.yml) Os registos históricos destas operações estão disponíveis tanto no portal como através do CLI. | [Operações de fornecedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az monitor de atividade-log](/cli/azure/monitor/activity-log)
| Registo e auditoria de planos de dados | Não | O plano de dados para o Serviço de Aplicações é uma partilha de ficheiros remotos contendo o conteúdo do site implantado por um cliente.  Não há auditoria da partilha remota de ficheiros. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas |  Documentação
|---|---|--|
| Autenticação| Sim | Os clientes podem construir aplicações no Serviço de Aplicações que se integram automaticamente com [o Azure Ative Directory (AZure AD),](../active-directory/index.yml) bem como outros fornecedores de identidade compatíveis com a OAuth Para acesso à gestão de ativos do App Service, todo o acesso é controlado por uma combinação de Azure AD autenticado principal e Azure RBAC. | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)
| Autorização| Sim | Para o acesso à gestão dos ativos do App Service, todo o acesso é controlado por uma combinação de Azure AD autenticado principal e Azure RBAC.  | [Autenticação e autorização no Serviço de Aplicações do Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | O conteúdo dos ficheiros do Web site é armazenado no Azure Storage, que encripta automaticamente o conteúdo em repouso. <br><br>Os segredos fornecidos pelo cliente são encriptados em repouso. Os segredos são encriptados em repouso enquanto armazenados nas bases de dados de configuração do Serviço de Aplicações.<br><br>Os discos ligados localmente podem ser utilizados opcionalmente como armazenamento temporário por websites (D:\local e %TMP%). Os discos ligados localmente não são encriptados em repouso. | [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | Os clientes podem optar por armazenar segredos de aplicações no Key Vault e recuperá-los em tempo de execução. | [Utilize referências do Cofre de Chaves para Serviço de Aplicações e Funções Azure (pré-visualização)](app-service-key-vault-references.md)
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Sim | Os clientes podem configurar web sites para exigir e usar HTTPS para tráfego de entrada.  | [Como fazer um Serviço de Aplicações Azure HTTPS apenas](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (blog post)
| Chamadas da API encriptadas| Sim | As chamadas de gestão para configurar o Serviço de Aplicações ocorrem através de chamadas [do Azure Resource Manager](../azure-resource-manager/index.yml) através de HTTPS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração do Serviço de Aplicações pode ser exportado como um modelo de Gestor de Recursos Azure e ver versão ao longo do tempo. Para operações de tempo de execução, os clientes podem manter várias versões ao vivo diferentes de uma aplicação utilizando a funcionalidade de implementação do Serviço de Aplicações. | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)