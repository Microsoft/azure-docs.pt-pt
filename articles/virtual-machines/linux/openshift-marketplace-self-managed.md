---
title: Implementar a oferta do Marketplace automática centrada no conceito de plataforma de contentores do OpenShift no Azure | Documentos da Microsoft
description: Implemente a oferta do Marketplace automática centrada no conceito de plataforma de contentores do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233094"
---
# <a name="configure-prerequisites"></a>Configurar os pré-requisitos

Antes de utilizar a oferta do Marketplace para implementar um cluster de OpenShift Container Platform gestão automática centrado no Azure, tem de configurar alguns pré-requisitos.  Leitura a [pré-requisitos do OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) artigo para obter instruções para criar um ssh chave (sem uma frase de acesso), o Cofre de chaves do Azure, o segredo do Cofre de chaves e um principal de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implementar com a oferta do Marketplace

A forma mais simples de implementar um cluster do OpenShift Container Platform gestão automática centrado no Azure está a utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Esta opção é a mais simples, mas ele também tem limitada capacidades de personalização. A oferta do Marketplace implementa o OpenShift Container Platform 3.11.82 e inclui as seguintes opções de configuração:

- **Nós do mestre**: Tipo de mestre de nós de três (3) com a instância configurável.
- **Infra-estrutura nós**: Tipo de três (3) infra-estrutura nós com a instância configurável.
- **Nós**: O número de nós (entre 1 e 9) e o tipo de instância é configurável.
- **Tipo de disco**: Discos geridos são utilizados.
- **Funcionamento em rede**: Suporte para a rede nova ou existente e o intervalo CIDR personalizado.
- **CNS**: CNS pode ser ativado.
- **Métricas**: Métricas hawkular podem ser ativadas.
- **Registo**: Registo de EFK pode ser ativado.
- **Fornecedor de Cloud do Azure**: Ativado por predefinição, pode ser desativado.

No canto superior esquerdo do portal do Azure, clique em **criar um recurso**, introduza "openshift container platform" na caixa de pesquisa e pressione Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Abre-se a página de resultados com **Red Hat OpenShift Container Platform Self-Managed** na lista. 

   ![Resultado de pesquisa do novo recurso](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique a oferta para ver os detalhes da oferta. Para implementar esta oferta, clique em **criar**. Será apresentada a interface do Usuário para introduzir os parâmetros necessários. A primeira tela é o **Noções básicas** painel.

   ![Página de ofertas de título](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda em qualquer um dos parâmetros de entrada, Paire o rato sobre o ***eu*** junto ao nome do parâmetro.

Introduza valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de utilizador administrador da VM | O utilizador de administrador a ser criada em todas as instâncias VM |
| SSH chave pública para o utilizador de Admin | Chave pública do SSH utilizada para iniciar sessão na VM - não tem de ter uma frase de acesso |
| Subscrição | Subscrição do Azure para implementar o cluster numa |
| Grupo de Recursos | Criar um novo grupo de recursos ou selecione um grupo de recursos vazio existente para os recursos de cluster |
| Location | Região do Azure para implementar o cluster numa |

   ![Painel de noções básicas da oferta](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Definições de infraestrutura**

Introduza valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo do nome do Cluster OCP | O prefixo utilizado para configurar nomes de anfitriões para todos os nós do cluster. Entre 1 e 20 carateres |
| Tamanho do nó principal | Aceite o tamanho VM predefinido ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM adequado para sua carga de trabalho |
| Tamanho do nó de infraestrutura | Aceite o tamanho VM predefinido ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM adequado para sua carga de trabalho |
| Número de nós de aplicação | Aceite o tamanho VM predefinido ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM adequado para sua carga de trabalho |
| Tamanho de nó da aplicação | Aceite o tamanho VM predefinido ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM adequado para sua carga de trabalho |
| Tamanho de anfitrião de bastião | Aceite o tamanho VM predefinido ou clique em **alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM adequado para sua carga de trabalho |
| Rede Virtual nova ou existente | Criar uma nova vNet (predefinição) ou utilizar uma vNet existente |
| Escolha o CIDR predefinições ou personalizar o intervalo de IP (CIDR) | Aceitar os intervalos CIDR predefinidos ou selecione **intervalo de IP personalizada** e introduza as informações de CIDR personalizadas.  Predefinições irão criar a vNet com o CIDR de 10.0.0.0/14, sub-rede principal com 10.1.0.0/16, infra-estrutura sub-rede com 10.2.0.0/16 e a sub-rede de computação e cns com 10.3.0.0/16 |
| Nome do grupo de recursos do Cofre de chaves | O nome do grupo de recursos que contém o Key Vault |
| Nome do Key Vault | O nome do Cofre de chaves que contém o segredo com o ssh chave privada.  Apenas carateres alfanuméricos e travessões permitidos em ter entre 3 e 24 carateres |
| Nome do segredo | O nome do segredo do que contém o ssh chave privada.  São permitidos apenas carateres alfanuméricos e travessões |

   ![Painel de infraestrutura de oferta](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar tamanho**

Para selecionar um tamanho VM diferente, clique em ***alterar o tamanho***.  Abre a janela de seleção de VM.  Selecione o tamanho da VM que pretende e clique em **selecione**.

   ![Selecione o tamanho da VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de rede Virtual existente | Nome da vNet existente |
| Nome da sub-rede para nós principais | Nome da sub-rede existente para nós principais.  Tem de conter, pelo menos, 16 endereços IP e siga o RFC 1918 |
| Nome de sub-rede para infra-estrutura nós | Nome do existente sub-rede para infra-estrutura de nós.  Tem de conter pelo menos 32 endereços IP e siga o RFC 1918 |
| Nome da sub-rede para nós de computação e cns | Nome da sub-rede existente para nós de computação e cns.  Tem de conter pelo menos 32 endereços IP e siga o RFC 1918 |
| Grupo de recursos para a rede Virtual existente | Nome do grupo de recursos que contém a vNet existente |

   ![Oferecer a vnet existente de infraestrutura](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Intervalo de IP personalizado**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de endereços da rede Virtual | Custom CIDR para a vNet |
| Intervalo de endereços da sub-rede que contêm os nós principais | Custom CIDR para sub-rede principal |
| Intervalo de endereços da sub-rede que contêm os nós de infraestrutura | Custom CIDR para a sub-rede de infraestrutura |
| Intervalo de endereços de sub-rede que contém os nós de computação e cns | CIDR personalizado para os nós de computação e cns |

   ![Intervalo IP personalizado de infraestrutura da oferta](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**Plataforma de contentores do OpenShift**

Introduza valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Palavra-passe de utilizador do administrador do OpenShift | Palavra-passe para o utilizador OpenShift inicial.  Este utilizador também será o administrador de cluster |
| Confirmar palavra-passe de utilizador do administrador do OpenShift | Volte a escrever a palavra-passe de utilizador do administrador do OpenShift |
| Nome de utilizador do Red Hat Gestor de subscrições | Nome de utilizador para aceder à sua subscrição do Red Hat ou a ID da organização.  Esta credencial é utilizada para registar a instância do RHEL à sua subscrição e não será armazenado pela Microsoft ou do Red Hat |
| Senha de usuário do Red Hat Gestor de subscrições | Palavra-passe para aceder à sua subscrição do Red Hat ou a chave de ativação.  Esta credencial é utilizada para registar a instância do RHEL à sua subscrição e não será armazenado pela Microsoft ou do Red Hat |
| ID do conjunto de OpenShift do Red Hat Gestor de subscrições | ID do conjunto que contém a elegibilidade do OpenShift Container Platform. Certifique-se de que tem suficiente elegibilidade do OpenShift Container Platform para a instalação do cluster |
| ID do conjunto de OpenShift do Red Hat Gestor de subscrições para Broker / dominar nós | Agrupamento ID que contém as elegibilidades do OpenShift Container Platform Broker / nós do mestre. Certifique-se de que tem suficiente elegibilidade do OpenShift Container Platform para a instalação do cluster. Se não utilizar o mediador / dominar o ID do conjunto, introduza o ID do conjunto de nós de aplicação |
| Configurar o fornecedor de Cloud do Azure | Configure o OpenShift para utilizar o fornecedor de Cloud do Azure. Necessário se o disco do Azure ao utilizar anexar para volumes persistentes.  A predefinição é Sim |
| GUID do ID de cliente de Principal de serviço do AD do Azure | GUID do Azure Principal de serviço do AD cliente ID – também conhecido como AppID. Apenas necessário se configurar o fornecedor de Cloud do Azure definida como **Sim** |
| Segredo do ID de cliente de Principal de serviço do AD do Azure | Segredo de ID do cliente de Principal de serviço do AD do Azure. Apenas necessário se configurar o fornecedor de Cloud do Azure definida como **Sim** |
 
   ![Painel de OpenShift da oferta](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Definições adicionais**

O painel de definições adicionais permite que a configuração da CNS para armazenamento de glusterfs, domínio de Router Sub, métricas e registo.  A predefinição não conseguem instalar qualquer uma destas opções e irá utilizar nip.io como o subdomínio de router para fins de teste. Ativar CNS irá instalar três nós de computação adicionais com três discos anexados adicionais que irão alojar glusterfs pods.  

Introduza valores para os parâmetros de entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configurar o contentor de armazenamento nativo (CNS) | Instala CNS o OpenShift de cluster e ativá-lo como armazenamento. Será o padrão se o fornecedor do Azure está desabilitado |
| Configurar o registo de Cluster | Instala a funcionalidade de registo de EFK ao cluster.  Infra-estrutura de tamanho nós adequadamente para pods EFK do anfitrião |
| Configurar métricas para o Cluster | Instala métricas Hawkular no cluster do OpenShift.  Infra-estrutura de tamanho nós adequadamente para pods de Hawkular métricas de anfitrião |
| Domínio de Router Sub predefinido | Selecione nipio para fins de teste ou personalizadas para introduzir o seu próprio domínio sub para produção |
 
   ![Painel adicional da oferta](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Definições adicionais - parâmetros Extras**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| (CNS) Tamanho do nó | Aceite o tamanho de nó predefinidos ou selecione **alterar o tamanho** para selecionar um novo tamanho VM |
| Introduza o subdomínio personalizado | O domínio de encaminhamento personalizado para ser usado para expor aplicativos via o router no OpenShift cluster.  Certifique-se de que criar a entrada DNS com carateres universais apropriado] |
 
   ![Oferecer cns adicionais instalar](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre nesta fase para verificar a quota de núcleos é suficiente para implementar o número total de VMs selecionadas para o cluster.  Reveja todos os parâmetros que foram introduzidos.  Se as entradas são aceitáveis, clique em **OK** para continuar.

   ![Painel de resumo de oferta](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contacto na página de compra e clique em **Compra** para aceitar os termos de utilização e iniciar a implementação do cluster OpenShift Container Platform.

   ![Painel de compra da oferta](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Ligue ao cluster do OpenShift

Quando termina, a implantação, obter a ligação da secção de saída da implementação. Ligar à consola do OpenShift com o seu browser, utilizando o **URL de Console do OpenShift**. Pode também SSH para o anfitrião de bastião. Segue-se um exemplo em que o nome de utilizador do administrador é clusteradmin e o IP público de bastion DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o [eliminação do grupo de az](/cli/azure/group) comando para remover o grupo de recursos, o OpenShift cluster e todos os recursos relacionados quando já não forem necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos Seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)

