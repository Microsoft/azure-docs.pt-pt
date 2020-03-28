---
description: incluir ficheiro
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 5439de30b02b0ce05853c8112f9e29239743ef98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67184879"
---
1. No seu navegador, abra a [imagem do Azure Marketplace para jenkins.](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)

1. Selecione **OBTÊ-lo agora**.

    ![Selecione GIT IT NOW para iniciar o processo de instalação para a imagem do Mercado Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Depois de rever os detalhes e informações sobre os termos de preços, selecione **Continuar**.

    ![Jenkins Marketplace preços de imagem e informações sobre termos.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Selecione **Criar** para configurar o servidor Jenkins no portal Azure. 

    ![Instale a imagem do Mercado Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. No separador **Basics,** especifique os seguintes valores:

   - **Nome** - `Jenkins`Inserir .
   - **Nome** do utilizador - Introduza o nome de utilizador a utilizar ao iniciar sessão na máquina virtual em que o Jenkins está a funcionar. O nome de utilizador tem de cumprir [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - **Tipo de autenticação** - Selecione **tecla pública SSH**.
   - **Chave pública SSH** - Copiar e colar uma chave pública RSA `ssh-rsa`em formato de linha única (começando por ) ou formato PEM multi-linhas. Pode gerar chaves SSH utilizando ssh-keygen no Linux e macOS, ou PuTTYGen no Windows. Para mais informações sobre as teclas SSH e Azure, consulte o artigo, [Como utilizar as teclas SSH com Windows on Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Subscrição** - Selecione a subscrição Azure na qual pretende instalar o Jenkins.
   - **Grupo de recursos** - Selecione **Criar novo,** e insira um nome para o grupo de recursos que serve de recipiente lógico para a recolha de recursos que compõem a sua instalação Jenkins.
   - **Localização** - Selecione **East US**.

     ![Introduza informações de autenticação e grupo de recursos para jenkins no separador Basic.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Selecione **OK** para seguir para o separador **Definições Adicionais.** 

1. No separador **Definições Adicionais,** especifique os seguintes valores:

   - **Tamanho** - Selecione a opção de tamanho apropriado para a sua máquina virtual Jenkins.
   - Tipo de **disco VM** - Especifique o HDD (disco rígido) ou o SSD (unidade de estado sólido) para indicar qual o tipo de disco de armazenamento permitido para a máquina virtual Jenkins.
   - **Rede virtual** - (Opcional) Selecione **a rede Virtual** para modificar as definições predefinidas.
   - **Subnets** - Selecione **Subnets,** verifique as informações e selecione **OK**.
   - **Endereço IP público** - O nome do endereço IP predefinido para o nome Jenkins especificado na página anterior com um sufixo de -IP. Pode selecionar a opção de alterar esse padrão.
   - **Etiqueta de nome** de domínio - Especifique o valor para o URL totalmente qualificado para a máquina virtual Jenkins.
   - **Tipo** de lançamento jenkins - Selecione `LTS`o `Weekly build`tipo `Azure Verified`de libertação desejado a partir das opções: , ou . As `LTS` `Weekly build` opções e opções são explicadas no artigo, [Jenkins LTS Release Line](https://jenkins.io/download/lts/). A `Azure Verified` opção refere-se a uma [versão Jenkins LTS](https://jenkins.io/download/lts/) que foi verificada para ser executada no Azure. 
   - **JDK Type** - JDK a instalar. Padrão é Zulu testado, construções certificadas de OpenJDK.

     ![Introduza as definições da máquina virtual para jenkins no separador Definições.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Selecione **OK** para seguir para o separador Definições de **Integração.**

1. No separador Definições de **Integração,** especifique os seguintes valores:

    - **Serviço Principal** - O diretor de serviço é adicionado ao Jenkins como uma credencial para autenticação com azure. `Auto`significa que o principal será criado pela MSI (Identidade de Serviço Gerido). `Manual`significa que o diretor deve ser criado por si. 
        - ID de **aplicação** e `Manual` **Segredo** - Se selecionar a opção para `Secret` a opção Principal de **Serviço,** terá de especificar o e para o `Application ID` seu diretor de serviço. Ao criar um diretor de [serviço,](/cli/azure/create-an-azure-service-principal-azure-cli)note que a função padrão é **a Contributiva,** o que é suficiente para trabalhar com os recursos do Azure.
    - **Ativar agentes cloud** - Especifique `ACI` o modelo de nuvem `VM` padrão para agentes onde se refere a Instância de Contentores Azure, e refere-se a máquinas virtuais. Também pode `No` especificar se não pretende ativar um agente de nuvem.

1. Selecione **OK** para passar ao separador **Resumo.**

1. Quando o separador **Resumo** aparece, a informação inserida é validada. Assim que vir a mensagem **de validação passada** (na parte superior do separador), selecione **OK**. 

     ![O separador Resumo exibe e valida as suas opções selecionadas.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Quando o separador **Criar** for exibido, selecione **Criar** para criar a máquina virtual Jenkins. Quando o seu servidor estiver pronto, uma notificação mostra no portal Azure.

     ![Jenkins está pronto para a notificação.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
