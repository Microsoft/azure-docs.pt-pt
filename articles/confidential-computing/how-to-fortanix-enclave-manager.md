---
title: Como - Executar uma aplicação com Fortanix Enclave Manager
description: Saiba como usar o Gestor do Enclave de Fortanix para converter as suas imagens contentorizadas
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 235f4eb236e144d41ffb9958b09dab0cf5c269b6
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462433"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>Como: Executar uma aplicação com Fortanix Enclave Manager 

Comece a executar a sua aplicação na computação confidencial Azure usando [Fortanix Enclave Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) e [Fortanix Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) da [Fortanix](https://www.fortanix.com/).


Fortanix é um fornecedor de software de terceiros com produtos e serviços construídos em cima da infraestrutura Azure. Existem outros fornecedores de terceiros que oferecem serviços de computação confidencial semelhantes no Azure.

> [!Note] 
 > Os produtos referenciados neste documento não estão sob o controlo da Microsoft. A Microsoft está a fornecer-lhe estas informações apenas como uma conveniência, e a referência a estes produtos não Microsoft não implica o endosso da Microsoft.



Este tutorial mostra-lhe como converter a sua imagem de aplicação numa imagem confidencial protegida por computação. Este ambiente utiliza o software [Fortanix,](https://www.fortanix.com/) alimentado por máquinas virtuais da Série DCsv2 da Azure. Esta solução orquestra políticas críticas de segurança, tais como verificação de identidade e controlo de acesso a dados.

 Para o apoio específico de Fortanix, junte-se à [comunidade Fortanix Slack](https://fortanix.com/community/) e use o canal #enclavemanager.


## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma conta do Fortanix Enclave Manager, [inscreva-se](https://em.fortanix.com/auth/sign-up) antes de começar.
1. Um registo privado do [Docker](https://docs.docker.com/) para empurrar imagens de aplicações convertidas.
1. Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Adicione uma aplicação ao Fortanix Enclave Manager
1. Inscreva-se no [Fortanix Enclave Manager (Fortanix EM)](https://em.fortanix.com)
1. Navegue na página **Contas** e selecione **ADD ACCOUNT** para criar uma nova conta. 
    
![Criar uma conta](media/how-to-fortanix-enclave-manager/create-account.png)

1. Depois de a sua conta ser criada, bastar para **selecionar** a conta recém-criada. Agora podemos começar a inscrever os nós computacional e criar aplicações. 
1. Selecione o botão **+ APPLICATION** para adicionar uma aplicação. Neste exemplo, vamos adicionar uma aplicação de Sistema Operativo Enclave Do Server Flask. 

1. Selecione o botão **ADD** para a aplicação Enclave OS. 

    ![Adicionar aplicação](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > Este tutorial abrange apenas a adição de Aplicações Enclave OS. [Leia mais](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) sobre como levar as aplicações da EDP Rust ao Fortanix Enclave Manager. 

6. Neste tutorial, usaremos o registo de estivadores de Fortanix para a aplicação da amostra. Preencha os detalhes das seguintes informações. Use o seu registo privado de estivadores para manter a imagem de saída. 
 
    - **Nome da aplicação**: Python Application Server
    - **Descrição**: Python Flask Server
    - **Nome da imagem de entrada**: fortanix/python-flask
    - **Nome da imagem de saída**: fortanx-private/python-flask-sgx
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Tamanho da memória**: 1 GB
    - **Contagem de fios**: 128

    *Opcional*: Executar o pedido.
    - **Estivador Hub:**[https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Aplicação**: fortanix/python-flask

        Execute o seguinte comando:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Adicione um certificado. Preencha as informações utilizando os detalhes abaixo e, em seguida, selecione **NEXT**:
    - **Domínio**: myapp.domain.dom
    - **Tipo**: Certificado emitido pelo Gestor do Enclave 
    - **Caminho chave**: /appkey.pem
    - **Tipo chave**: RSA
    - **Percurso do certificado**: /appcert.pem
    - **Tamanho da chave RSA**: 2048 Bits
    

## <a name="create-an-image"></a>Criar uma imagem
A Fortanix EM Image é uma versão ou versão de software de uma aplicação. Cada imagem está associada a um haxixe enclave (MRENCLAVE). 
1. Na página **'Adicionar Imagem',** insira as **credenciais de REGISTO** para o nome de imagem de **saída**. Estas credenciais são usadas para aceder ao registo privado de estivadores onde a imagem será empurrada. 

    ![criar imagem](media/how-to-fortanix-enclave-manager/create-image.png)
1. Forneça a etiqueta de imagem e selecione **Criar**.

    ![adicionar tag](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Domínio e Imagem permitem listagem 
Uma aplicação cujo domínio é adicionado à lista de autorizações, receberá um Certificado TLS do Fortanix Enclave Manager. Da mesma forma, quando uma aplicação é executado a partir da imagem convertida, tentará contactar o Fortanix Enclave Manager. O pedido irá então pedir um Certificado TLS. 

Mude para o separador **Tarefas** à esquerda e aprove os pedidos pendentes para permitir o domínio e a imagem. 

## <a name="enroll-compute-node-agent-in-azure"></a>Inscreva-se no agente de nó de computação em Azure

### <a name="generate-and-copy-join-token"></a>Gerar e copiar Juntar ficha
Em Fortanix Enclave Manager, você vai criar um símbolo. Este token permite que um nó computativo em Azure se autense. Terá de dar este símbolo à sua máquina virtual Azure.
1. Na consola de gestão, selecione o botão **+ INSCREVER NODE.** 
1. Selecione **GENERATE TOKEN** para gerar o token de Junção. Copie o símbolo.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Inscreva nós no Agente fortanix no Mercado Azure

A criação de um Agente De Nó Fortanix irá implementar uma máquina virtual, interface de rede, rede virtual, grupo de segurança de rede e um endereço IP público no seu grupo de recursos Azure. A sua subscrição Azure será cobrada de hora em hora para a máquina virtual. Antes de criar um Agente fortanix node, reveja a [página de preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Azure para dCsv2-Series. Elimine os recursos do Azure quando não estiver a ser utilizado. 

1. Vá ao [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) e assine com as suas credenciais Azure.
1. Na barra de pesquisa, escreva **Fortanix Confidencial Computação Node Agent**. Selecione a App que aparece na caixa de pesquisa chamada **Fortanix Confidential Computing Node Agent** para navegar para a página inicial da oferta. 
     ![mercado de pesquisa](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Selecione **Get It Now**, preencha as suas informações se necessário e selecione **Continue**. Será redirecionado para o portal Azure. 
1. Selecione **Criar** para introduzir a página de implementação do Agente de Computação Confidencial Fortanix.
1. Nesta página, você estará inserindo informações para implementar uma máquina virtual. Especificamente, este VM é uma máquina virtual ativada pela Série SCsv2 intel SGX da Azure com o software Fortanix Node Agent instalado. O Agente Nó permitirá que a sua imagem convertida seja executada de forma segura nos nós Intel SGX em Azure.  Selecione o grupo **de subscrição** e **recursos** onde pretende implementar a máquina virtual e os recursos associados. 
 
    > [!NOTE]
    > Existem constrangimentos ao implementar máquinas virtuais da Série DCsv2 em Azure. Poderá ter de solicitar uma quota para núcleos adicionais. Leia sobre [soluções de computação confidencial em VMs Azure](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solutions) para mais informações. 

1. Selecione uma região disponível.
1. Introduza um nome para a sua máquina virtual no campo **Nome do Nó.** 
1. Introduza e nome de utilizador e senha (ou Chave SSH) para autenticação na máquina virtual.
1. Deixe o tamanho do disco oss predefinido como 200 e selecione um tamanho VM (Standard_DC4s_v2 será suficiente para este tutorial)
1. Cole o símbolo gerado anteriormente no campo **Join Token.**

     ![implementar recurso](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. Selecione **Review + Criar**. Certifique-se de que a validação passa e, em seguida, **selecione Criar**. Uma vez que todos os recursos são implantados, o nó de computação está agora inscrito no Gestor do Enclave. 

## <a name="run-the-application-image-on-the-compute-node"></a>Executar a imagem da aplicação no nó de computação
Execute a aplicação executando o seguinte comando. Certifique-se de que altera o Nome de Imagem DO Nó, Porta e Imagem Convertido como entradas para a sua aplicação específica. 
 
Neste tutorial, o comando a executar é:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

onde, 
- *52.152.206.164* é o IP do agente de nó
- *9092* é a porta que o Agente Nó escuta
- *fortanix-private/python-flask-sgx* é a aplicação convertida que pode ser encontrada no separador Imagens sob a coluna **Image Name** na tabela **Imagens** no Portal Web Fortanix Enclave Manage. 
    
## <a name="verify-and-monitor-the-running-application"></a>Verifique e monitorize a aplicação de execução
1. Volte para o [Fortanix Enclave Manager](https://em.fortanix.com/console)
1. Certifique-se de que está a trabalhar dentro da **Conta** onde inscreveu o nó
1. Navegue para a **Consola de Gestão** selecionando o ícone superior no painel de navegação esquerdo. 
1. Selecione o **separador 'Aplicação'**
1. Verifique se há uma aplicação em execução com um nó computacional associado


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode eliminar o grupo de recursos, a máquina virtual e os recursos associados. A eliminação do grupo de recursos irá desenrolar os nós associados à sua imagem convertida. 

Selecione o grupo de recursos para a máquina virtual e, em seguida, **selecione Delete**. Confirme o nome do grupo de recursos para terminar a eliminação dos recursos.

Para eliminar a Conta Gestora do Enclave fortanix que criou, vá à [Página de Contas](https://em.fortanix.com/accounts) no Gestor do Enclave. Sobressaia a conta que pretende eliminar. Selecione os pontos pretos verticais no canto superior direito e selecione **Eliminar Conta**.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou a ferramenta Fortanix para converter a sua imagem de aplicação para funcionar em cima de uma máquina virtual de computação confidencial. Para obter mais informações sobre máquinas virtuais de computação confidencial no Azure, consulte [Soluções em Máquinas Virtuais.](virtual-machine-solutions.md) 

Para saber mais sobre as ofertas confidenciais de computação da Azure, consulte [a Visão Geral da computação confidencial do Azure](overview.md)

 Saiba como completar tarefas semelhantes utilizando outras ofertas de terceiros em Azure, como [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) e [Scone.](https://sconedocs.github.io)  