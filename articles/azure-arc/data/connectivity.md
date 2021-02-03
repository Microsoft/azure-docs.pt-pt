---
title: Modos e requisitos de conectividade
description: Explica que o Azure Arc permitiu opções de conectividade de serviços de dados para a partir do seu ambiente para Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493977"
---
# <a name="connectivity-modes-and-requirements"></a>Modos e requisitos de conectividade

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Modos de conectividade

Existem múltiplas opções para o grau de conectividade do seu Azure Arc permitiu o ambiente de serviços de dados para a Azure. Como os seus requisitos variam com base na política de negócios, regulação do governo ou disponibilidade de conectividade de rede para Azure, você pode escolher entre os seguintes modos de conectividade.

Os serviços de dados habilitados a Azure Arc oferecem-lhe a opção de ligar ao Azure em dois *modos de conectividade diferentes:* 

- Diretamente ligado 
- Indiretamente ligado

O modo de conectividade proporciona-lhe a flexibilidade para escolher a quantidade de dados enviados para o Azure e como os utilizadores interagem com o Controlador de Dados do Arco. Dependendo do modo de conectividade escolhido, alguma funcionalidade do Azure Arc habilitado para os serviços de dados pode ou não estar disponível.

Importante, se os serviços de dados habilitados a Azure Arc estiverem diretamente ligados ao Azure, então os utilizadores podem utilizar [APIs do Gestor de Recursos Azure,](/rest/api/resources/)o CLI Azure e o portal Azure para operar os serviços de dados do Azure Arc. A experiência em modo conectado diretamente é muito semelhante à forma como utilizaria qualquer outro serviço Azure com provisionamento/desavisionamento, escala, configuração, e assim por diante no portal Azure.  Se os serviços de dados habilitados a Azure Arc estiver indiretamente ligados ao Azure, então o portal Azure é uma visão apenas de leitura. Pode ver o inventário de instâncias geridas pela SQL e as instâncias de Hiperescala Postgres que implementou e os detalhes sobre eles, mas não pode tomar medidas sobre elas no portal Azure.  No modo indiretamente conectado, todas as ações devem ser tomadas localmente usando Azure Data Studio, as [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ferramentas nativas de , ou Kubernetes como kubectl.

Além disso, o Azure Ative Directory e o Azure Role-Based Access Control só podem ser utilizados no modo ligado diretamente apenas porque existe uma dependência de uma ligação contínua e direta ao Azure para fornecer esta funcionalidade.

Alguns serviços ligados ao Azure só estão disponíveis quando podem ser contactados diretamente, tais como os serviços de segurança Azure Defender, Container Insights e Azure Backup para o armazenamento de bolhas.

||**Indiretamente ligado**|**Diretamente ligado**|**Nunca ligado**|
|---|---|---|---|
|**Descrição**|O modo indiretomente conectado oferece a maioria dos serviços de gestão localmente no seu ambiente sem ligação direta ao Azure.  Uma quantidade mínima de dados deve ser enviada para a Azure _apenas_ para efeitos de inventário e faturação . É exportado para um ficheiro e enviado para Azure pelo menos uma vez por mês.  Não é necessária qualquer ligação direta ou contínua ao Azure.  Algumas funcionalidades e serviços que requerem uma ligação ao Azure não estarão disponíveis.|O modo ligado diretamente oferece todos os serviços disponíveis quando uma ligação direta pode ser estabelecida com o Azure. As ligações são sempre iniciadas _do_ seu ambiente para Azure e utilizam portas e protocolos padrão como HTTPS/443.|Nenhum dado pode ser enviado de ou para Azure de qualquer forma.|
|**Disponibilidade atual**| Disponível em pré-visualização.|Disponível em pré-visualização.|Não apoiado atualmente.|
|**Casos de uso típicos**|Centros de dados no local que não permitem a conectividade dentro ou fora da região de dados do centro de dados devido a políticas de conformidade empresarial ou regulamentar ou por preocupações de ataques externos ou exfiltração de dados.  Exemplos típicos: Instituições financeiras, cuidados de saúde, governo. <br/><br/>Locais de edge site onde o site edge normalmente não tem conectividade com a Internet.  Exemplos típicos: aplicações de petróleo/gás ou de campo militar.  <br/><br/>Localizações do site edge que têm conectividade intermitente com longos períodos de interrupções.  Exemplos típicos: estádios, navios de cruzeiro. | Organizações que usam nuvens públicas.  Exemplos típicos: Azure, AWS ou Google Cloud.<br/><br/>Locais de borda onde a conectividade da Internet é tipicamente presente e permitida.  Exemplos típicos: lojas de retalho, fabrico.<br/><br/>Centros de dados corporativos com políticas mais permissivas de conectividade de/para a sua região de dados do datacenter para a Internet.  Exemplos típicos: Empresas não regulamentadas, pequenas e médias empresas|Ambientes verdadeiramente "abertos ao ar" onde nenhum dado em nenhuma circunstância pode vir ou sair do ambiente de dados. Exemplos típicos: instalações governamentais ultrassecretas.|
|**Como os dados são enviados para Azure**|Existem três opções para como os dados de faturação e inventário podem ser enviados para a Azure:<br><br> 1) Os dados são exportados para fora da região de dados por um processo automatizado que tem conectividade tanto com a região de dados seguras como com o Azure.<br><br>2) Os dados são exportados para fora da região de dados por um processo automatizado dentro da região de dados, copiado automaticamente para uma região menos segura, e um processo automatizado na região menos segura envia os dados para a Azure.<br><br>3) Os dados são exportados manualmente por um utilizador dentro da região segura, trazidos manualmente para fora da região segura e enviados manualmente para Azure. <br><br>As duas primeiras opções são um processo contínuo automatizado que pode ser programado para funcionar com frequência, pelo que há um atraso mínimo na transferência de dados para a Azure sujeito apenas à conectividade disponível para o Azure.|Os dados são enviados automaticamente e continuamente para a Azure.|Os dados nunca são enviados para o Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Disponibilidade de recursos por modo de conectividade

|**Funcionalidade**|**Indiretamente ligado**|**Diretamente ligado**|
|---|---|---|
|**Alta disponibilidade automática**|Suportado|Suportado|
|**Prestação de self-service**|Suportado<br/>A criação pode ser feita através do Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ou ferramentas nativas de Kubernetes (helm, kubectl, oc, etc.), ou usando o fornecimento de GitOps de Azure Arc.|Suportado<br/>Além das opções de criação de modo indiretamente conectado, também pode criar através do portal Azure Resource Manager APIs, dos modelos Azure CLI ou ARM. **Pendente de disponibilidade do modo ligado diretamente**
|**Escalabilidade elástica**|Suportado|Suportado<br/>**Pendente de disponibilidade do modo ligado diretamente**|
|**Faturação**|Suportado<br/>Os dados de faturação são periodicamente exportados e enviados para Azure.|Suportado<br/>Os dados de faturação são enviados automaticamente e continuamente para a Azure e refletidos em tempo real. **Pendente de disponibilidade do modo ligado diretamente**|
|**Gestão de inventário**|Suportado<br/>Os dados de inventário são periodicamente exportados e enviados para Azure.<br/><br/>Use ferramentas de cliente como Azure Data Studio, Azure Data CLI, ou `kubectl` para ver e gerir o inventário localmente.|Suportado<br/>Os dados de inventário são enviados automaticamente e continuamente para a Azure e refletidos em tempo real. Como tal, pode gerir o inventário diretamente a partir do portal Azure. **Pendente de disponibilidade do modo ligado diretamente**|
|**Atualizações automáticas e remendos**|Suportado<br/>O controlador de dados deve ter acesso direto ao Registo de Contentores da Microsoft (MCR) ou as imagens do contentor devem ser retiradas da MCR e empurradas para um registo local de contentores privados a que o responsável pelo tratamento de dados tem acesso.|Suportado<br/>**Pendente de disponibilidade do modo ligado diretamente**|
|**Backup automático e restauro**|Suportado<br/>Cópia de segurança local automática e restauro.|Suportado<br/>Além de cópias de segurança locais automatizadas e restauro, pode _opcionalmente_ enviar backups para a Azure Backup para retenção fora do local a longo prazo. **Pendente de disponibilidade do modo ligado diretamente**|
|**Monitorização**|Suportado<br/>Monitorização local utilizando tabliers Grafana e Kibana.|Suportado<br/>Além dos dashboards de monitorização locais, pode enviar _opcionalmente_ dados de monitorização e registos para o Azure Monitor para monitorização em escala de vários sites num só local. **Pendente de disponibilidade do modo ligado diretamente**|
|**Autenticação**|Utilize o nome de utilizador/palavra-passe local para o tratamento de dados e a autenticação do painel de instrumentos. Utilize logins SQL e Postgres ou Ative Directory para conectividade com casos de base de dados.  Utilize fornecedores de autenticação K8s para autenticação na API de Kubernetes.|Além dos métodos de autenticação para o modo indiretamente ligado, pode utilizar _opcionalmente_ o Azure Ative Directory. **Pendente de disponibilidade do modo ligado diretamente**|
|**Controlo de acesso baseado em funções (RBAC)**|Use Kubernetes RBAC na API de Kubernetes. Utilize SQL e Postgres RBAC para casos de base de dados.|Pode integrar opcionalmente o Azure Ative Directory e o Azure RBAC. **Pendente de disponibilidade do modo ligado diretamente**|
|**Azure Defender**|Não suportado|Planejado para o futuro|

## <a name="connectivity-requirements"></a>Requisitos de conectividade

**Algumas funcionalidades requerem uma ligação ao Azure.**

**Toda a comunicação com o Azure é sempre iniciada a partir do seu ambiente.** Isto é verdade mesmo para as operações, que são iniciadas por um utilizador no portal Azure.  Nesse caso, existe efetivamente uma tarefa, que está em fila em Azure.  Um agente no seu ambiente inicia a comunicação com o Azure para ver que tarefas estão na fila, executa as tarefas e reporta o estado/conclusão/falha em Azure.

|**Tipo de Dados**|**Direção**|**Obrigatório/Opcional**|**Custos Adicionais**|**Modo Necessário**|**Notas**|
|---|---|---|---|---|---|
|**Imagens de contentor**|Registo de contentores da Microsoft -> Cliente|Necessário|Não|Indireta ou direta|As imagens de contentores são o método para distribuir o software.  Num ambiente que pode ligar-se ao Registo de Contentores da Microsoft (MCR) através da Internet, as imagens do contentor podem ser retiradas diretamente da MCR.  No caso de o ambiente de implantação não ter conectividade direta, pode retirar as imagens da MCR e empurrá-las para um registo privado de contentores no ambiente de implantação.  No momento da criação, pode configurar o processo de criação para retirar do registo privado de contentores em vez de MCR. Isto também se aplica a atualizações automatizadas.|
|**Inventário de recursos**|Ambiente de clientes - > Azure|Necessário|Não|Indireta ou direta|Um inventário de controladores de dados, casos de base de dados (PostgreSQL e SQL) é mantido em Azure para efeitos de faturação e também para fins de criação de um inventário de todos os controladores de dados e instâncias de base de dados em um lugar que é especialmente útil se você tiver mais de um ambiente com serviços de dados Azure Arc.  À medida que os casos são aprovisionados, desprovisionados, dimensionados para fora/para dentro, o inventário é atualizado em Azure.|
|**Faturação de dados de telemetria**|Ambiente de clientes - > Azure|Necessário|Não|Indireta ou direta|A utilização de casos de base de dados deve ser enviada para a Azure para efeitos de faturação.  Não há qualquer custo para os serviços de dados habilitados a Azure Arc durante o período de pré-visualização.|
|**Dados de monitorização e registos**|Ambiente de clientes - > Azure|Opcional|Talvez dependendo do volume de dados (ver [preços do Azure Monitor)](https://azure.microsoft.com/en-us/pricing/details/monitor/)|Indireta ou direta|É melhor enviar os dados e registos de monitorização recolhidos localmente para o Azure Monitor para agregar dados em vários ambientes num só local e também para utilizar os serviços do Azure Monitor como alertas, utilizando os dados em Azure Machine Learning, etc.|
|**Controlo de Acesso baseado em funções Azure (Azure RBAC)**|Ambiente de clientes -> Azure -> Ambiente de Clientes|Opcional|Não|Apenas direto|Se quiser utilizar o Azure RBAC, então a conectividade deve ser estabelecida com o Azure em todos os momentos.  Se não quiser usar o Azure RBAC, então o LOCAL Kubernetes RBAC pode ser usado.  **Pendente de disponibilidade do modo ligado diretamente**|
|**Azure Active Directory (AD)**|Ambiente de cliente -> Ambiente de clientes Azure ->|Opcional|Talvez, mas já pode estar a pagar pelo Azure AD.|Apenas direto|Se pretender utilizar o Azure AD para autenticação, então a conectividade deve ser sempre estabelecida com o Azure. Se não quiser utilizar o Azure AD para autenticação, pode nos dar serviços da Federação De Diretório Ativo (ADFS) sobre o Ative Directory. **Pendente de disponibilidade do modo ligado diretamente**|
|**Backup e restauro**|Ambiente de cliente -> Ambiente de Cliente|Necessário|Não|Direto ou indireto|O serviço de backup e restauro pode ser configurado para apontar para as aulas de armazenamento local. |
|**Azure backup - retenção a longo prazo**| Ambiente de clientes - > Azure | Opcional| Sim para armazenamento Azure | Apenas direto |Você pode querer enviar backups que são levados localmente para Azure Backup para retenção de backups a longo prazo, fora do local e trazê-los de volta para o ambiente local para restaurar. **Pendente de disponibilidade do modo ligado diretamente**|
|**Serviços de segurança Azure Defender**|Ambiente de cliente -> Ambiente de clientes Azure ->|Opcional|Sim|Apenas direto|**Pendente de disponibilidade do modo ligado diretamente**|
|**Alterações de provisionamento e configuração a partir do portal Azure**|Ambiente de cliente -> Ambiente de clientes Azure ->|Opcional|Não|Apenas direto|As alterações de provisão e configuração podem ser feitas localmente através do Azure Data Studio ou do [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  No modo ligado diretamente, também poderá providenciar e fazer alterações de configuração a partir do portal Azure. **Pendente de disponibilidade do modo ligado diretamente**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Detalhes sobre endereços de internet, portas, encriptação e suporte a servidor de procuração

Atualmente, na fase de pré-visualização, apenas o modo indiretamente ligado é suportado. Neste modo, existem apenas três ligações necessárias aos serviços disponíveis na Internet. Estas ligações incluem:

- [Registo de Contentores da Microsoft (MCR)](#microsoft-container-registry-mcr)
- [APIs do Azure Resource Manager](#azure-resource-manager-apis)
- [Azure monitor APIs](#azure-monitor-apis)

Todas as ligações HTTPS ao Azure e ao Registo de Contentores da Microsoft são encriptadas utilizando SSL/TLS utilizando certificados oficialmente assinados e verificáveis.

As seguintes secções fornecem detalhes para estas ligações. 

### <a name="microsoft-container-registry-mcr"></a>Registo de Contentores da Microsoft (MCR)

O Registo de Contentores da Microsoft acolhe imagens de contentores de serviços de dados ativadas pelo Azure Arc.  Pode retirar estas imagens da MCR e empurrá-las para um registo privado de contentores e configurar o processo de implantação do controlador de dados para retirar as imagens do contentor desse registo privado do contentor.

#### <a name="connection-source"></a>Origem de ligação

O kubernetes kubelet em cada um dos nós Kubernetes puxando as imagens do recipiente.

#### <a name="connection-target"></a>Destino de ligação

`mcr.microsoft.com`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Pode usar proxy

Sim

#### <a name="authentication"></a>Autenticação

Nenhum

### <a name="azure-resource-manager-apis"></a>APIs do Azure Resource Manager
O Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] e o Azure CLI conectam-se às APIs do Gestor de Recursos Azure para enviar e recuperar dados de e para a Azure para algumas funcionalidades.

#### <a name="connection-source"></a>Origem de ligação

Um computador que executa o Azure Data [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Studio, ou Azure CLI que está a ligar-se ao Azure.

#### <a name="connection-target"></a>Destino de ligação

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Pode usar proxy

Sim

#### <a name="authentication"></a>Autenticação 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure monitor APIs

O Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] e o Azure CLI conectam-se às APIs do Gestor de Recursos Azure para enviar e recuperar dados de e para a Azure para algumas funcionalidades.

#### <a name="connection-source"></a>Origem de ligação

Um computador em execução [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ou Azure CLI que está a carregar métricas de monitorização ou registos para o Azure Monitor.

#### <a name="connection-target"></a>Destino de ligação

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Pode usar proxy

Sim

#### <a name="authentication"></a>Autenticação 

Azure Active Directory

> [!NOTE]
> Por enquanto, todas as ligações HTTPS/443 do navegador para os dashboards Grafana e Kibana e do controlador de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] dados API são encriptadas com certificados auto-assinados.  No futuro, estará disponível uma funcionalidade que lhe permitirá fornecer os seus próprios certificados para encriptação destas ligações SSL.

A conectividade do Azure Data Studio e [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] do servidor API da Kubernetes utiliza a autenticação e encriptação de Kubernetes que estabeleceu.  Cada utilizador que esteja a utilizar o Azure Data Studio e [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] deve ter uma ligação autenticada à API de Kubernetes para executar muitas das ações relacionadas com os serviços de dados habilitados a Azure Arc.

