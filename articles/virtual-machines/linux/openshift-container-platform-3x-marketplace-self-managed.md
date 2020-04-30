---
title: Implementar plataforma de contentores OpenShift 3.11 Oferta de Mercado Autogerida em Azure
description: Implemente a Plataforma de Contentores OpenShift 3.11 Oferta de Mercado Autogerida em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759569"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de utilizar a oferta do Marketplace para implementar um cluster openshift autogerido plataforma 3.11 em Azure, alguns pré-requisitos devem ser configurados.  Leia o artigo [de pré-requisitos openShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) para instruções para criar uma chave ssh (sem uma frase de passe), cofre chave Azure, segredo do cofre chave e um diretor de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implementar usando a oferta marketplace

A forma mais simples de implantar um cluster OpenShift Container Platform 3.11 autogerido em Azure é utilizar a oferta do [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Esta opção é a mais simples, mas também tem capacidades de personalização limitadas. A oferta do Marketplace implementa a Plataforma de Contentores OpenShift 3.11.82 e inclui as seguintes opções de configuração:

- **Nós Mestres**: Três (3) Nós Mestrecom tipo de instância configurável.
- **Pontos de infravermelhos:** Três (3) Nós de infravermelhos com tipo de instância configurável.
- **Nós**: O número de nós (entre 1 e 9) e o tipo de instância são configuráveis.
- **Tipo de disco**: São utilizados discos geridos.
- **Rede**: Suporte para uma rede nova ou existente e gama CIDR personalizada.
- **CNS**: O SNC pode ser ativado.
- **Métricas:** As Métricas Hawkular podem ser ativadas.
- **Exploração madeireira**: A exploração madeireira EFK pode ser ativada.
- **Fornecedor de nuvem azure**: Ativado por padrão, pode ser desativado.

Na parte superior esquerda do portal Azure, clique **em Criar um recurso**, introduza a "plataforma de contentores de turno aberto" na caixa de pesquisa e bata no Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página Resultados abrirá com a Plataforma de **Contentores OpenShift 3.11** da Red Hat Na lista. 

   ![Novo resultado da pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para ver detalhes da oferta. Para implementar esta oferta, clique em **Criar**. Aparecerão os parâmetros necessários para entrar. O primeiro ecrã é a lâmina **Basics.**

   ![Página de título de oferta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda em qualquer um dos parâmetros de entrada, paire sobre o ***i*** ao lado do nome do parâmetro.

Introduza os valores para os parâmetros de entrada e clique **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome do utilizador do administrador VM | O utilizador administrador a ser criado em todos os casos de VM |
| Chave Pública SSH para utilizador de administrador | Chave pública SSH usada para entrar em VM - não deve ter uma palavra-passe |
| Subscrição | Assinatura Azure para implantar cluster em |
| Grupo de Recursos | Criar um novo grupo de recursos ou selecionar um grupo de recursos vazios existente para recursos de cluster |
| Localização | Região de Azure para implantar cluster em |

   ![Oferecer lâmina básica](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Definições de infraestrutura**

Introduza os valores para os parâmetros de entrada e clique **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo de nome do cluster OCP | Prefixo de cluster usado para configurar nomes de anfitriões para todos os nós. Entre 1 e 20 caracteres |
| Tamanho do nó mestre | Aceite o tamanho de VM predefinido ou clique em Alterar o **tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho de VM apropriado para a sua carga de trabalho |
| Tamanho do nó de infraestrutura | Aceite o tamanho de VM predefinido ou clique em Alterar o **tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho de VM apropriado para a sua carga de trabalho |
| Número de nós de aplicação | Aceite o tamanho de VM predefinido ou clique em Alterar o **tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho de VM apropriado para a sua carga de trabalho |
| Tamanho do nó de aplicação | Aceite o tamanho de VM predefinido ou clique em Alterar o **tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho de VM apropriado para a sua carga de trabalho |
| Tamanho do hospedeiro do bastião | Aceite o tamanho de VM predefinido ou clique em Alterar o **tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho de VM apropriado para a sua carga de trabalho |
| Rede Virtual Nova ou Existente | Crie um novo vNet (Padrão) ou use um vNet existente |
| Escolha as definições do CIDR predefinidos ou personalize o INTERVALO IP (CIDR) | Aceite intervalos DE CIDR padrão ou selecione **Gama IP Personalizada** e introduza informações cidr personalizadas.  Definições predefinidas criarão vNet com CIDR de 10.0.0.0.0/14, sub-rede master com 10.1.0.0.0/16, subnet de infra-rede com 10.2.0.0.0/16, e subnet computação e cns com 10.3.0.0/16 |
| Nome do grupo de recursos do cofre chave | O nome do Grupo de Recursos que contém o Cofre chave |
| Nome do cofre da chave | O nome do Cofre-Chave que contém o segredo com a chave privada ssh.  Apenas personagens alfanuméricos e traços são permitidos, e estar entre 3 e 24 caracteres |
| Nome Secreto | O nome do segredo que contém a chave privada ssh.  Apenas personagens alfanuméricos e traços são permitidos |

   ![Oferecer lâmina de infraestrutura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar tamanho**

Para selecionar um tamanho VM diferente, clique em ***Alterar o tamanho***.  A janela de seleção VM abrirá.  Selecione o tamanho VM que deseja e clique **em Selecionar**.

   ![Selecione Tamanho VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual Existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de rede virtual existente | Nome da vNet existente |
| Nome de sub-rede para nomes mestres | Nome da subnet existente para os narizes-mestre.  Precisa conter pelo menos 16 endereços IP e seguir rFC 1918 |
| Nome de sub-rede para nódeos de infravermelhos | Nome da sub-rede existente para os nódosos de infravermelhos.  Precisa conter pelo menos 32 endereços IP e seguir rFC 1918 |
| Nome de sub-rede para computação e nódoas cns | Nome da subnet existente para os nósoculas e os nódosos do SNC.  Precisa conter pelo menos 32 endereços IP e seguir rFC 1918 |
| Grupo de Recursos para a rede virtual existente | Nome do grupo de recursos que contém o vNet existente |

   ![Oferecer infraestrutura existente vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Gama IP Personalizada**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Gama de endereços para a Rede Virtual | CIDR personalizado para a vNet |
| Gama de endereços para a subnet que contém os nódosos mestres | CIDR personalizado para master subnet |
| Gama de endereços para a subnet que contém os nódosos de infraestrutura | CIDR personalizado para subnet de infraestrutura |
| Gama de endereços para subnet contendo os nósocula e os nódosos do SNC | CIDR personalizado para os nósoculas e cns |

   ![Oferecer gama IP personalizada de infraestrutura](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Introduza valores para os parâmetros de entrada e clique EM **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Palavra-passe do utilizador do administrador OpenShift | Palavra-passe para o utilizador openshift inicial.  Este utilizador também será o administrador do cluster |
| Confirmar palavra-passe do utilizador do anúncio openshift | Reescreva a palavra-passe do utilizador do administrador OpenShift |
| Nome do utilizador do gestor de subscrição do chapéu vermelho | Nome do utilizador para aceder à sua Subscrição ou ID da Organização do Chapéu Vermelho.  Esta credencial é utilizada para registar a instância RHEL na sua subscrição e não será armazenada pela Microsoft ou pela Red Hat |
| Palavra-passe do gestor de subscrição do chapéu vermelho | Palavra-passe para aceder à sua chave de subscrição ou de ativação do Chapéu Vermelho.  Esta credencial é utilizada para registar a instância RHEL na sua subscrição e não será armazenada pela Microsoft ou pela Red Hat |
| Red Hat Subscription Manager OpenShift Pool ID | ID do pool que contém o direito da plataforma de contentores OpenShift. Certifique-se de que tem direitos suficientes da Plataforma de Contentores OpenShift para a instalação do cluster |
| Red Hat Subscription Manager OpenShift Pool ID para Corretor / Master Nodes | ID do pool que contém direitos da Plataforma de Contentores OpenShift para Broker / Master Nodes. Certifique-se de que tem direitos suficientes da Plataforma de Contentores OpenShift para a instalação do cluster. Se não utilizar o broker /master pool ID, insira o ID da piscina para os nós de aplicação |
| Configure Azure Cloud Provider | Configure o OpenShift para utilizar o Fornecedor de Nuvem Azure. Necessário se utilizar a fixação do disco Azure para volumes persistentes.  Padrão é Sim |
| Azure AD Service Principal ID CLIENTE GUID | Azure AD Service Principal CLIENTE ID GUID - também conhecido como AppID. Só é necessário se configurar o Fornecedor de Nuvem Azure definido para **Sim** |
| Azure AD Service Principal Cliente ID Secret | Azure AD Service Principal Cliente ID Secret. Só é necessário se configurar o Fornecedor de Nuvem Azure definido para **Sim** |
 
   ![Oferecer lâmina OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Configurações adicionais**

A lâmina de definições adicionais permite a configuração de CNS para armazenamento de glusterfs, login, métricas e sub domínio router.  O predefinido não instala nenhuma destas opções e utilizará nip.io como subdomínio do router para fins de teste. Ativar o CNS irá instalar três nódeos de cálculo adicionais com três discos adicionais anexados que irão alojar cápsulas de glusterfs.  

Introduza valores para os parâmetros de entrada e clique EM **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configure Armazenamento Nativo de Recipiente (CNS) | Instala o SNc no cluster OpenShift e ativa-o como armazenamento. Será por defeito se o Fornecedor Azure estiver desativado |
| Configure Cluster Logging | Instala a funcionalidade de registo EFK no cluster.  Nósde os tamanhos de infravermelhos adequadamente para hospedar cápsulas EFK |
| Configurar métricas para o cluster | Instala métricas Hawkular no cluster OpenShift.  Tamanho de nódeos de infravermelhos adequadamente para hospedar cápsulas de métricas Hawkular |
| Domínio sub do Router padrão | Selecione nipio para testar ou personalizado para entrar no seu próprio subdomínio para produção |
 
   ![Oferecer lâmina adicional](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Configurações adicionais - Parâmetros extra**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| (CNS) Tamanho do nó | Aceite o tamanho do nó predefinido ou selecione **Alterar tamanho** para selecionar um novo tamanho VM |
| Introduza o seu subdomínio personalizado | O domínio de encaminhamento personalizado a utilizar para expor aplicações através do router no cluster OpenShift.  Certifique-se de criar a entrada adequada do DNS wildcard] |
 
   ![Oferecer instalação adicional do CNS](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre nesta fase para verificar que a quota central é suficiente para implantar o número total de VMs selecionados para o cluster.  Reveja todos os parâmetros que foram introduzidos.  Se as inputs forem aceitáveis, clique em **OK** para continuar.

   ![Oferecer lâmina de resumo](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contacto na página Comprar e clique em **Comprar** para aceitar os termos de utilização e iniciar a implementação do cluster da Plataforma de Contentores OpenShift.

   ![Oferecer lâmina de compra](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Ligue-se ao cluster OpenShift

Quando a implantação terminar, recupere a ligação da secção de saída da implantação. Conecte-se à consola OpenShift com o seu navegador utilizando o URL da **Consola OpenShift**. também pode sSH para o anfitrião bastião. Segue-se um exemplo em que o nome de utilizador do administrador é clusteradmin e o bastião público IP DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Use o [grupo AZ eliminar](/cli/azure/group) o comando para remover o grupo de recursos, o cluster OpenShift, e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Implantação openShift de resolução de problemas em Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Começar com plataforma de contentores OpenShift](https://docs.openshift.com)
- 