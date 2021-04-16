---
title: Matriz de atribuição de responsabilidade aberta do chapéu vermelho azulado
description: Conheça a propriedade das responsabilidades pelo funcionamento de um cluster Azure Red Hat OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, chapéu vermelho, cli, RACI, apoio
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537007"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Visão geral das responsabilidades para Azure Red Hat OpenShift

Este documento descreve as responsabilidades da Microsoft, Red Hat e clientes para os clusters Azure Red Hat OpenShift. Para obter mais informações sobre o Azure Red Hat OpenShift e os seus componentes, consulte a Definição de Serviço De Abertura do Chapéu Vermelho Azure.

Enquanto a Microsoft e a Red Hat gerem o serviço Azure Red Hat OpenShift, o cliente partilha a responsabilidade pela funcionalidade do seu cluster. Enquanto os clusters Azure Red Hat OpenShift são hospedados em recursos Azure em subscrições do cliente Azure, eles são acedidos remotamente. A plataforma subjacente e a segurança de dados são propriedade da Microsoft e da Red Hat.

## <a name="overview"></a>Descrição Geral
<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Gestão de Incidentes e Operações</a></strong>
   </td>
   <td><strong><a href="#change-management">Gestão de Mudanças</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Gestão de Identidade e Acesso</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Conformidade de Segurança e Regulação</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Dados do cliente</a>
   </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Aplicações de clientes</a>
   </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Serviços de desenvolvimento </a>
   </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
   <td>Cliente </td>
  </tr>
  <tr>
   <td>Monitorização da plataforma </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Registo </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
  </tr>
  <tr>
   <td>Rede de aplicações </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Rede de agrupamentos </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Redes virtuais </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
   <td>Partilhado </td>
  </tr>
  <tr>
   <td>Nosdes de avião de controlo </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Nódoas operárias </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Versão cluster </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Partilhado </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Gestão de Capacidade </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Partilhado </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Armazenamento Virtual </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
  <tr>
   <td>Infraestruturas Físicas e Segurança </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
   <td>Microsoft e Chapéu Vermelho </td>
  </tr>
</table>


Tabela 1. Responsabilidades por recurso


## <a name="tasks-for-shared-responsibilities-by-area"></a>Tarefas para responsabilidades partilhadas por área 

### <a name="incident-and-operations-management"></a>Gestão de incidentes e operações 

O cliente e a Microsoft e a Red Hat partilham a responsabilidade pela monitorização e manutenção de um cluster Azure Red Hat OpenShift. O cliente é responsável pela gestão de incidentes e operações dos dados de [aplicação](#customer-data-and-applications) do cliente e de qualquer rede personalizada que o cliente possa ter configurado.

<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong>Responsabilidades da Microsoft e do Chapéu Vermelho</strong>
   </td>
   <td><strong>Responsabilidades do cliente</strong>
   </td>
  </tr>
  <tr>
   <td>Rede de aplicações </td>
   <td>
<ul>

<li>Monitorize o serviço de balanceadores de carga em nuvem e o serviço de router openshift nativo e responda aos alertas.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorizar a saúde dos pontos finais do balanceador de carga de serviço.

<li>Monitorize a saúde das rotas de aplicação, e os pontos finais por trás delas.

<li>Reportar falhas na Microsoft e no Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Redes virtuais
   </td>
   <td>
<ul>

<li>Monitorize os equilibradores de carga em nuvem, sub-redes e componentes de nuvem Azure necessários para a rede de plataformas predefinidas e responda aos alertas.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorize o tráfego de rede que é configurado opcionalmente via VNet para a ligação VNet, ligação VPN ou ligação private link para potenciais problemas ou ameaças de segurança.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 2. Responsabilidades partilhadas pela gestão de incidentes e operações


### <a name="change-management"></a>Gestão de Alterações

A Microsoft e a Red Hat são responsáveis por permitir alterações na infraestrutura e serviços do cluster que o cliente irá controlar, bem como manter versões disponíveis para os nós principais, serviços de infraestrutura e nós de trabalhadores. O cliente é responsável por iniciar alterações de infraestruturas e instalar e manter serviços opcionais e configurações de rede no cluster, bem como todas as alterações aos dados do cliente e aplicações ao cliente.


<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong>Responsabilidades da Microsoft e do Chapéu Vermelho</strong>
   </td>
   <td><strong>Responsabilidades do cliente</strong>
   </td>
  </tr>
  <tr>
   <td>Registo </td>
   <td>
<ul>

<li>Centralmente agregado e monitor de registos de auditoria da plataforma.

<li>Forneça documentação para que o cliente possa permitir a registo de aplicações utilizando o Log Analytics através do Azure Monitor para contentores.

<li>Forneça registos de auditoria a pedido do cliente.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Instale o operador de registo de aplicações predefinido opcional no cluster.

<li>Instale, configuure e mantenha quaisquer soluções de registo de aplicações opcionais, tais como contentores sidecareiros ou aplicações de registo de terceiros.

<li>Sintonize o tamanho e a frequência dos registos de aplicações que estão a ser produzidos pelas aplicações do cliente se estiverem a afetar a estabilidade do cluster.

<li>Solicite registos de auditoria da plataforma através de um caso de suporte para pesquisa de incidentes específicos.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rede de aplicações
   </td>
   <td>
<ul>

<li>Configurar equilibradores de carga de nuvem pública

<li>Configurar o serviço de router openshift nativo. Forneça a capacidade de configurar o router como privado e adicione um fragmento de router adicional.

<li>Instale, configuure e mantenha os componentes SDN OpenShift para o tráfego interno de cápsulas predefinidos.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configure permissões de rede de pod não padrão para redes de projetos e pod, entrada de casulos e saída de pod usando objetos NetworkPolicy.

<li>Solicitar e configurar quaisquer equilibrentes adicionais de carga de serviço para serviços específicos.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rede de agrupamentos
   </td>
   <td>
<ul>

<li>Configurar componentes de gestão de clusters, tais como pontos finais de serviço público ou privado e integração necessária com componentes de rede virtuais.

<li>Configurar componentes de rede internos necessários para a comunicação interna do cluster entre os nós operários e mestres.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Fornecer intervalos de endereço IP não predefinidos opcionais para a máquina CIDR, serviço CIDR e POD CIDR, se necessário através do OpenShift Cluster Manager quando o cluster for a provisionado.

<li>Solicitando que o ponto final do serviço API seja tornado público ou privado na criação de agrupamentos ou após a criação de clusters através do Azure CLI.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Redes virtuais
   </td>
   <td>
<ul>

<li>Configurar e configurar componentes de rede virtuais necessários para o fornecimento do cluster, incluindo nuvem privada virtual, sub-redes, equilibradores de carga, gateways de internet, gateways NAT, etc.

<li>Forneça a capacidade para o cliente gerir a conectividade VPN com recursos no local, conectividade VNet para VNet e conectividade Private Link, conforme exigido através do OpenShift Cluster Manager.

<li>Permitir que os clientes criem e implementem esquiliadores públicos de carga em nuvem para utilização com equilibradores de carga de serviço.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurar e manter componentes de rede de nuvem pública opcionais, tais como a ligação VNet à VNet, a ligação VPN ou a ligação Private Link.

<li>Solicitar e configurar quaisquer equilibrentes adicionais de carga de serviço para serviços específicos.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Versão cluster
   </td>
   <td>
<ul>

<li>Comunicar horário e estado das atualizações para versões menores e de manutenção

<li>Publique alterlogs e notas de lançamento para atualizações menores e de manutenção
</li>
</ul>
   </td>
   <td>
<ul>

<li>Iniciar atualização do cluster

<li>Testar aplicações de clientes em versões menores e de manutenção para garantir a compatibilidade
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Gestão de Capacidade
   </td>
   <td>
<ul>

<li>Monitorização da utilização do plano de controlo (nós principais)

<li>Balança e/ou redimensionar os nós dos planos de controlo para manter a qualidade do serviço

<li>Monitorizar a utilização dos recursos do cliente, incluindo capacidade de Rede, Armazenamento e Computação. Quando as funcionalidades de autoscalagem não forem ativadas, alerte o cliente para quaisquer alterações necessárias aos recursos de cluster (por exemplo. novos nós de computação à escala, armazenamento adicional, etc.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Utilize os controlos do Gestor de Cluster OpenShift fornecidos para adicionar ou remover os nós adicionais do trabalhador, conforme necessário.

<li>Responda às notificações da Microsoft e da Red Hat sobre os requisitos de recursos do cluster.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 3. Responsabilidades partilhadas para a gestão da mudança


### <a name="identity-and-access-management"></a>Gestão de Acesso e Identidades 

A gestão da identidade e do acesso inclui todas as responsabilidades para garantir que apenas os indivíduos adequados tenham acesso a clusters, aplicações e recursos de infraestrutura. Isto inclui tarefas como fornecer mecanismos de controlo de acesso, autenticação, autorização e gestão do acesso aos recursos.


<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong>Responsabilidades da Microsoft e do Chapéu Vermelho</strong>
   </td>
   <td><strong>Responsabilidades do cliente</strong>
   </td>
  </tr>
  <tr>
   <td>Registo </td>
   <td>
<ul>

<li>Aderir a um processo de acesso interno baseado em padrões da indústria para registos de auditoria de plataforma.

<li>Fornecer capacidades nativas de RBAC aberto.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configure o RBAC openshift para controlar o acesso a projetos e por extensão dos registos de candidatura de um projeto.

<li>Para soluções de registo de aplicações de terceiros ou personalizadas, o cliente é responsável pela gestão do acesso.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rede de aplicações
   </td>
   <td>
<ul>

<li>Fornecer capacidades nativas de RBAC aberto.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configure o RBAC openshift para controlar o acesso à configuração da rota, conforme necessário.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rede de agrupamentos
   </td>
   <td>
<ul>

<li>Fornecer capacidades nativas de RBAC aberto.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gerir a organização red hat membro das contas da Red Hat.

<li>Gerencie a Org Admins para a organização Red Hat para conceder acesso ao OpenShift Cluster Manager.

<li>Configure o RBAC openshift para controlar o acesso à configuração da rota, conforme necessário.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Redes virtuais
   </td>
   <td>
<ul>

<li>Fornecer controlos de acesso ao cliente através do OpenShift Cluster Manager.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gerir o acesso opcional do utilizador a componentes de nuvem pública através do OpenShift Cluster Manager.
</li>
</ul>
   </td>
  </tr>
</table>


Mesa 4. Responsabilidades partilhadas na gestão da identidade e do acesso


### <a name="security-and-regulation-compliance"></a>Conformidade com a segurança e regulamentação 

A segurança e o cumprimento incluem quaisquer responsabilidades e controlos que garantam o cumprimento das leis, políticas e regulamentos relevantes.


<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong>Responsabilidades da Microsoft e do Chapéu Vermelho</strong>
   </td>
   <td><strong>Responsabilidades do cliente</strong>
   </td>
  </tr>
  <tr>
   <td>Registo </td>
   <td>
<ul>

<li>Envie registos de auditoria de cluster para um MICROSOFT e Red Hat SIEM para analisar eventos de segurança. Reter registos de auditoria por um período de tempo definido para apoiar a análise forense.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analise os registos de aplicações para eventos de segurança. Envie registos de aplicações para um ponto final externo através de contentores sidecareiros ou aplicações de registo de terceiros se for necessária uma retenção mais longa do que a oferecida pela pilha de registo predefinido.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Redes virtuais
   </td>
   <td>
<ul>

<li>Monitorize componentes de rede virtuais para potenciais problemas e ameaças à segurança.

<li>Utilize ferramentas adicionais da Microsoft e do Red Hat Azure para monitorização e proteção adicionais.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorize opcionalmente componentes de rede virtuais configurados para potenciais problemas e ameaças à segurança.

<li>Configure as regras de firewall necessárias ou as proteções do centro de dados, conforme necessário.
</li>
</ul>
   </td>
  </tr>
</table>


Tabela 5. Responsabilidades partilhadas pelo cumprimento da segurança e da regulamentação


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Responsabilidades do cliente ao utilizar o Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Dados e aplicações do cliente

O cliente é responsável pelas aplicações, cargas de trabalho e dados que implementa para o Azure Red Hat OpenShift. No entanto, a Microsoft e a Red Hat fornecem várias ferramentas para ajudar o cliente a gerir dados e aplicações na plataforma.


<table>
  <tr>
   <td><strong>Recurso</strong>
   </td>
   <td><strong>Como a Microsoft e o Red Hat ajudam</strong>
   </td>
   <td><strong>Responsabilidades do cliente</strong>
   </td>
  </tr>
  <tr>
   <td>Dados do Cliente </td>
   <td>
<ul>

<li>Mantenha padrões de nível de plataforma para encriptação de dados, tal como definidos pelos padrões de segurança e conformidade da indústria. 

<li>Forneça componentes OpenShift para ajudar a gerir dados de aplicações, tais como segredos.

<li>Permitir a integração com serviços de dados de terceiros (como o Azure SQL) para armazenar e gerir dados fora do cluster e/ou Microsoft e Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Manter a responsabilidade por todos os dados do cliente armazenados na plataforma e como as aplicações do cliente consomem e expõem estes dados.

<li>Encriptação etcd
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Aplicações ao Cliente
   </td>
   <td>
<ul>

<li>Provision clusters com componentes OpenShift instalados para que os clientes possam aceder às APIs OpenShift e Kubernetes para implantar e gerir aplicações contentorizadas.

<li>Fornecer acesso a APIs OpenShift que um cliente pode usar para configurar operadores para adicionar serviços comunitários, de terceiros, Microsoft e Red Hat e Red Hat ao cluster. 

<li>Forneça aulas de armazenamento e plug-ins para suportar volumes persistentes para utilização com aplicações do cliente.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Manter a responsabilidade pelas aplicações, dados e dados completos do cliente e de terceiros.

<li>Se um cliente adicionar Red Hat, comunidade, terceiros, seus próprios ou outros serviços ao cluster, utilizando operadores ou imagens externas, o cliente é responsável por estes serviços e por trabalhar com o fornecedor apropriado (incluindo o Red Hat) para resolver quaisquer problemas.

<li>Utilizar as ferramentas e funcionalidades fornecidas para <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">configurar e implementar;</a> <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">manter-se atualizado;</a> <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">criar pedidos e limites</a>de recursos; <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">tamanho do cluster para ter recursos suficientes para executar apps;</a> <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">configurar permissões;</a> integrar-se com outros serviços; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">gerir quaisquer fluxos de imagem ou modelos que o cliente implemente;</a> <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">servir externamente;</a> salvar, fazer o back up e restaurar os dados; e, de outra forma, gerir as suas cargas de trabalho altamente disponíveis e resilientes.

<li>Manter a responsabilidade de monitorizar as aplicações executadas no Azure Red Hat OpenShift; incluindo instalar e operar software para recolher métricas e criar alertas.
</li>
</ul>
   </td>
  </tr>
</table>


Mesa 7. Responsabilidades do cliente pelos dados dos clientes, aplicações e serviços do cliente
