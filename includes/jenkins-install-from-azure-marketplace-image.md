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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124632"
---
1. No browser, abra a [imagem do Azure Marketplace do Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Selecione **obter agora**.

    ![Selecione TI do GIT agora para iniciar o processo de instalação para a imagem do Marketplace do Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Depois de rever as informações de termos e detalhes de preços, selecione **continuar**.

    ![Marketplace do Jenkins preços e termos de informações da imagem.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Selecione **criar** para configurar o servidor do Jenkins no portal do Azure. 

    ![Instale a imagem do Marketplace do Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Na **Noções básicas** separador, especifique os seguintes valores:

   - **Nome** -introduza `Jenkins`.
   - **Nome de utilizador** -introduza o nome de utilizador a utilizar quando iniciar sessão na máquina virtual no qual está a executar o Jenkins. O nome de utilizador tem de cumprir [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - **Tipo de autenticação** - selecione **chave pública SSH**.
   - **Chave pública SSH** -copiar e colar uma chave pública do RSA no formato de linha única (começando com `ssh-rsa`) ou o formato PEM com várias linha. Pode gerar chaves SSH com ssh-keygen no Linux e macOS ou PuTTYGen no Windows. Para obter mais informações sobre chaves SSH e o Azure, veja o artigo [como utilizar chaves SSH com Windows no Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Subscrição** -selecione a subscrição do Azure para o qual pretende instalar o Jenkins.
   - **Grupo de recursos** - selecione **criar novo**e introduza um nome para o grupo de recursos que funciona como um contentor lógico para a coleção de recursos que compõem a sua instalação do Jenkins.
   - **Localização** - selecione **E.U.A. Leste**.

     ![Introduza as informações do grupo de recursos e a autenticação do Jenkins no separador básico.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Selecione **OK** para avançar para o **definições adicionais** separador. 

1. Na **definições adicionais** separador, especifique os seguintes valores:

   - **Tamanho** -selecione a opção de dimensionamento apropriado para a máquina virtual do Jenkins.
   - **Tipo de disco VM** - Especifique qualquer um dos HDD (unidade de disco rígido) ou SSD (unidade de estado sólido) para indicar o tipo de disco de armazenamento é permitido para a máquina virtual do Jenkins.
   - **Rede virtual** -(opcional) selecione **rede Virtual** para modificar as configurações padrão.
   - **Sub-redes** - selecione **sub-redes**, verifique as informações e selecione **OK**.
   - **Endereço IP público** -nome do endereço de IP com o nome de Jenkins especificado na página anterior com um sufixo de - IP predefinido. Pode selecionar a opção para alterar esse padrão.
   - **Etiqueta de nome de domínio** -especificar o valor para o URL completamente qualificado para a máquina virtual do Jenkins.
   - **Tipo de versão do Jenkins** -selecione o tipo de versão desejada entre as opções: `LTS`, `Weekly build`, ou `Azure Verified`. O `LTS` e `Weekly build` opções são explicadas neste artigo, [linha de versão do Jenkins LTS](https://jenkins.io/download/lts/). O `Azure Verified` opção refere-se a uma [versão Jenkins LTS](https://jenkins.io/download/lts/) que foi verificada para execução no Azure. 
   - **Tipo de JDK** -JDK para ser instalado. A predefinição é o Zulu testado, compilações de certificados do OpenJDK.

     ![Introduza as definições de máquina virtual do Jenkins no separador Definições.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Selecione **OK** para avançar para o **definições de integração** separador.

1. Na **definições de integração** separador, especifique os seguintes valores:

    - **Principal de serviço** -o principal de serviço é adicionado no Jenkins como uma credencial para a autenticação com o Azure. `Auto` significa que o principal será criado por MSI (identidade do serviço gerido). `Manual` significa que a entidade de segurança deve ser criada por si. 
        - **ID da aplicação** e **segredo** - se de que seleciona o `Manual` opção para o **Principal de serviço** opção, terá de especificar o `Application ID` e `Secret` para seu principal de serviço. Quando [criar um principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli), tenha em atenção que a função predefinida é **contribuinte**, que é suficiente para trabalhar com recursos do Azure.
    - **Ativar os agentes de Cloud** -especifique o modelo de cloud predefinido para agentes em que `ACI` refere-se a instância de contentor do Azure, e `VM` refere-se às máquinas virtuais. Também pode especificar `No` se não pretender ativar um agente de cloud.

1. Selecione **OK** para avançar para o **resumo** separador.

1. Quando o **resumo** guia exibe, as informações de inserido são validadas. Quando vir o **a validação passada** mensagem (na parte superior do separador), selecione **OK**. 

     ![A guia resumida apresenta e valida as opções selecionadas.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Quando o **Create** separador apresenta, selecione **criar** para criar a máquina virtual do Jenkins. Quando o servidor estiver pronto, é apresentada uma notificação no portal do Azure.

     ![O Jenkins é notificação pronta.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
