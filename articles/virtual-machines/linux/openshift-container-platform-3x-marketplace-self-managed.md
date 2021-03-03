---
title: Implementar plataforma de contentores openshift 3.11 Self-Managed oferta de marketplace em Azure
description: Implementar a Plataforma de Contentores OpenShift 3.11 Self-Managed Oferta de Mercado em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670450"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de utilizar a oferta do Marketplace para implantar um cluster 3.11 da Plataforma de Contentores OpenShift auto-geridos em Azure, alguns pré-requisitos devem ser configurados.  Leia o artigo [pré-requisitos openShift](./openshift-container-platform-3x-prerequisites.md) para instruções para criar uma chave ssh (sem uma palavra-passe), cofre chave Azure, segredo de cofre chave, e um diretor de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implementar usando a oferta do Marketplace

A forma mais simples de implantar um cluster de contentores OpenShift auto-geridos 3.11 em Azure é utilizar a oferta do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Esta opção é a mais simples, mas também tem capacidades de personalização limitadas. A oferta do Marketplace implementa a Plataforma de Contentores OpenShift 3.11.82 e inclui as seguintes opções de configuração:

- **Nós Mestres**: Três (3) Nós Mestres com tipo de instância configurável.
- **Nó de infravermelhos**: Três (3) Nóns de infravermelhos com tipo de instância configurável.
- **Nós**: O número de nós (entre 1 e 9) e o tipo de instância são configuráveis.
- **Tipo de disco**: São utilizados discos geridos.
- **Rede**: Suporte para rede nova ou existente e gama CIDR personalizada.
- **CNS**: O SNC pode ser ativado.
- **Métricas**: As métricas hawkular podem ser ativadas.
- **Registo :** O registo EFK pode ser ativado.
- **Azure Cloud Provider**: Ativado por padrão, pode ser desativado.

Na parte superior esquerda do portal Azure, clique em **Criar um recurso,** introduza a "plataforma de contentores de abertura" na caixa de pesquisa e clique em Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página Resultados abrirá com **a Plataforma de Contentores OpenShift do Chapéu Vermelho 3.11 Auto-Gerida** na lista. 

   ![Novo resultado da pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para ver detalhes da oferta. Para implementar esta oferta, clique em **Criar**. Aparecerá o UI para introduzir os parâmetros necessários. O primeiro ecrã é a lâmina **Basics.**

   ![Oferta página de título](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda em qualquer um dos parâmetros de entrada, paire sobre o ***i*** ao lado do nome do parâmetro.

Introduza os valores para os parâmetros de entrada e clique **em OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de utilizador do administrador VM | O utilizador administrador a ser criado em todas as instâncias VM |
| Chave pública SSH para utilizador de administração | A chave pública SSH usada para iniciar sessão em VM - não deve ter uma palavra-passe |
| Subscrição | A subscrição do Azure para implantar cluster em |
| Grupo de Recursos | Criar um novo grupo de recursos ou selecionar um grupo de recursos vazios existente para recursos de cluster |
| Localização | Região de Azure para implantar cluster em |

   ![Oferecer lâmina básica](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Definições de infraestruturas**

Introduza os valores para os parâmetros de entrada e clique **em OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo do nome do cluster OCP | O prefixo do cluster usado para configurar os hostnames para todos os nós. Entre 1 e 20 caracteres |
| Tamanho do nó mestre | Aceite o tamanho VM predefinido ou clique em **Alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM apropriado para a sua carga de trabalho |
| Tamanho do nó de infraestrutura | Aceite o tamanho VM predefinido ou clique em **Alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM apropriado para a sua carga de trabalho |
| Número de nós de aplicação | Aceite o tamanho VM predefinido ou clique em **Alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM apropriado para a sua carga de trabalho |
| Tamanho do nó de aplicação | Aceite o tamanho VM predefinido ou clique em **Alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM apropriado para a sua carga de trabalho |
| Tamanho do hospedeiro de bastião | Aceite o tamanho VM predefinido ou clique em **Alterar o tamanho** para selecionar um tamanho VM diferente.  Selecione o tamanho VM apropriado para a sua carga de trabalho |
| Rede Virtual Nova ou Existente | Criar um novo vNet (Padrão) ou usar um vNet existente |
| Escolha configurações CIDR predefinidos ou personalize a gama IP (CIDR) | Aceite as gamas CIDR predefinidos ou selecione **o alcance IP personalizado** e introduza informações personalizadas do CIDR.  As Definições predefinidos criarão vNet com CIDR de 10.0.0.0/14, sub-rede master com 10.1.0.0/16, infra-rede com 10.2.0.0/16, e sub-redes de computação e cns com 10.3.0.0/16 |
| Nome do grupo de recursos do cofre chave | O nome do Grupo de Recursos que contém o Cofre de Chaves |
| Nome do cofre de chaves | O nome do Cofre-Chave que contém o segredo com a chave privada.  Apenas caracteres alfanuméricos e traços são permitidos, e estar entre 3 e 24 caracteres |
| Nome secreto | O nome do segredo que contém a chave privada ssh.  Apenas caracteres alfanuméricos e traços são permitidos |

   ![Oferecer lâmina de infraestrutura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Alterar tamanho**

Para selecionar um tamanho VM diferente, clique em ***Alterar o tamanho** _.  A janela de seleção VM abrir-se-á.  Selecione o tamanho VM que deseja e clique em _*Select**.

   ![Selecione tamanho VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome de rede virtual existente | Nome do vNet existente |
| Nome da sub-rede para os principais nosdes | Nome da sub-rede existente para nós mestres.  Precisa conter pelo menos 16 endereços IP e seguir RFC 1918 |
| Nome da sub-rede para nóns infravermelhos | Nome da sub-rede existente para os nós infra.  Precisa conter pelo menos 32 endereços IP e seguir RFC 1918 |
| Nome da sub-rede para nó de computação e cns | Nome da sub-rede existente para nó de computação e cns.  Precisa conter pelo menos 32 endereços IP e seguir RFC 1918 |
| Grupo de Recursos para a Rede Virtual existente | Nome do grupo de recursos que contém o vNet existente |

   ![Oferta de infraestrutura vnet existente](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Gama IP personalizada**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de endereços para a Rede Virtual | CIDR personalizado para o vNet |
| Intervalo de endereço para a sub-rede que contém os nosdes-mestre | CIDR personalizado para sub-rede master |
| Intervalo de endereço para a sub-rede que contém os nosdes de infraestrutura | CIDR personalizado para sub-rede de infraestrutura |
| Intervalo de endereços para sub-rede que contém os nó de computação e cns | CIDR personalizado para os nóndes de computação e cns |

   ![Oferta de infraestrutura custom IP gama](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Introduza os valores para os Parâmetros de Entrada e clique **em OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Senha de utilizador de administrador de turno aberto | Palavra-passe para o utilizador inicial OpenShift.  Este utilizador também será o administrador do cluster |
| Confirme a senha de utilizador do administrador de turno aberto | Retipe a senha de utilizador do administrador de turno aberto |
| Nome de utilizador do gestor de subscrição de chapéu vermelho | Nome do utilizador para aceder à subscrição do chapéu vermelho ou iD da organização.  Esta credencial é usada para registar a instância RHEL na sua subscrição e não será armazenada pela Microsoft ou pela Red Hat |
| Senha de utilizador do gestor de subscrição de chapéu vermelho | Palavra-passe para aceder à subscrição ou chave de ativação do chapéu vermelho.  Esta credencial é usada para registar a instância RHEL na sua subscrição e não será armazenada pela Microsoft ou pela Red Hat |
| Gestor de assinatura de chapéu vermelho openshift pool id | ID do pool que contém direito da plataforma de recipientes openshift. Certifique-se de que tem direitos suficientes da Plataforma de Contentores OpenShift para a instalação do cluster |
| Gestor de subscrição de chapéu vermelho OpenShift Pool ID para Corretor / Master Nodes | ID do pool que contém direitos da plataforma de recipientes openshift para Corretor /Master Nodes. Certifique-se de que tem direitos suficientes da Plataforma de Contentores OpenShift para a instalação do cluster. Se não utilizar o ID do corretor /master pool, introduza o ID da piscina para nó de aplicação |
| Configure Fornecedor de nuvem Azure | Configure OpenShift para utilizar o Azure Cloud Provider. Necessário se utilizar o disco Azure se ligar para volumes persistentes.  O padrão é Sim |
| Azure Ad Service Principal Cliente ID GUID | Azure AD Service Principal Client ID GUID - também conhecido como AppID. Só é necessário se o Fornecedor de Nuvem Azure configurado para **Sim** |
| Azure Ad Service Principal Cliente ID Secret | Azure Ad Service Principal Cliente ID Secret. Só é necessário se o Fornecedor de Nuvem Azure configurado para **Sim** |
 
   ![Ofereça lâmina OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Definições adicionais**

A lâmina de Definições Adicionais permite a configuração do CNS para o armazenamento de glusterfs, registo, métricas e sub-router.  O padrão não instala nenhuma destas opções e utilizará nip.io como subdesemo do router para efeitos de teste. Permitir o CNS irá instalar três nós computacionais adicionais com três discos adicionais anexados que irão acolher cápsulas de glusterfs.  

Introduza os valores para os Parâmetros de Entrada e clique **em OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configure o armazenamento nativo do contentor (CNS) | Instala o SNC no cluster OpenShift e permite-o como armazenamento. Será padrão se O Fornecedor Azure for desativado |
| Configurar a madeira do cluster | Instala a funcionalidade de registo EFK no cluster.  Tamanho infravermelho acena adequadamente para hospedar cápsulas EFK |
| Métricas de configuração para o Cluster | Instala métricas Hawkular no cluster OpenShift.  Tamanho infravermelho acena adequadamente para hospedar cápsulas de métricas Hawkular |
| Sub domínio do router predefinido | Selecione nipio para testes ou personalizados para introduzir o seu próprio subd domínio para produção |
 
   ![Oferecer lâmina adicional](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Definições adicionais - Parâmetros extra**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| (CNS) Tamanho do nó | Aceite o tamanho do nó predefinido ou selecione **tamanho de alteração** para selecionar um novo tamanho VM |
| Insira o seu subdomínio personalizado | O domínio de encaminhamento personalizado a ser utilizado para expor aplicações através do router no cluster OpenShift.  Certifique-se de criar a entrada de DNS wildcard apropriada] |
 
   ![Oferecer instalação adicional do CNS](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre nesta fase para verificar a quota principal é suficiente para implantar o número total de VMs selecionados para o cluster.  Reveja todos os parâmetros que foram introduzidos.  Se as entradas forem aceitáveis, clique **em OK** para continuar.

   ![Oferta lâmina de resumo](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contacto na página Comprar e clique em **Comprar** para aceitar os termos de utilização e iniciar a implementação do cluster da Plataforma de Contentores OpenShift.

   ![Oferta lâmina de compra](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Ligue-se ao cluster OpenShift

Quando a colocação terminar, recupere a ligação da secção de saída da implantação. Ligue a consola OpenShift ao seu navegador utilizando o **URL da consola OpenShift**. você também pode SSH para o anfitrião do Bastião. Segue-se um exemplo em que o nome de utilizador de administração é clusteradmin e o público de bastião IP DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar os recursos

Utilize o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Resolução de problemas OpenShift implantação em Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Começando com plataforma de recipientes openshift](https://docs.openshift.com)
- 
