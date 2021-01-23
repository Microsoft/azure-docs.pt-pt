---
title: Windows Virtual Desktop host pool Azure portal - Azure
description: Como criar um conjunto de anfitriões virtual do Windows desktop utilizando o portal Azure.
author: Heidilohr
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 268f1c4bff84a2c9ef67c3f6b5749bc8d0b90e85
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735185"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutorial: Criar uma piscina de anfitriões com o portal Azure

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Quaisquer objetos que crie com o Windows Virtual Desktop (clássico) não podem ser geridos com o portal Azure.

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas (VMs) dentro de ambientes de ambiente de secretária virtual do Windows. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

Este artigo irá acompanhá-lo através do processo de configuração para criar uma piscina de anfitriões para um ambiente de desktop virtual do Windows através do portal Azure. Este método fornece uma interface de utilizador baseada no navegador para criar um pool de anfitriões no Windows Virtual Desktop, criar um grupo de recursos com VMs numa subscrição Azure, juntar esses VMs ao domínio Azure Ative Directory (AD) e registar os VMs com o Windows Virtual Desktop.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará introduzir os seguintes parâmetros para criar uma piscina hospedeira:

- O nome de imagem VM
- Configuração VM
- Propriedades de domínio e rede
- Windows Virtual Desktop host pool properties

Também precisa de saber as seguintes coisas:

- Onde está a origem da imagem que pretende usar. É da Galeria Azure ou é uma imagem personalizada?
- O seu domínio junta-se a credenciais.

Além disso, certifique-se de que registou o fornecedor de recursos microsoft.DesktopVirtualization. Se ainda não o fez, vá a **Subscrições,** selecione o nome da sua subscrição e, em seguida, selecione **fornecedores de Recursos**. Procure por DesktopVirtualization, selecione Microsoft.DesktopVirtualization e, em seguida, selecione Register.

Quando criar uma piscina de anfitriões virtual do Windows com o modelo Azure Resource Manager, pode criar uma máquina virtual a partir da galeria Azure, uma imagem gerida ou uma imagem não gerida. Para saber mais sobre como criar imagens VM, consulte [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Crie uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/capture-image-resource.md).

Se ainda não tiver uma subscrição do Azure, certifique-se de [criar uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar a seguir estas instruções.

## <a name="begin-the-host-pool-setup-process"></a>Inicie o processo de configuração da piscina de anfitrião

Para começar a criar a sua nova piscina de anfitriões:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Se está a assinar o portal gov dos EUA, vá em [https://portal.azure.us/](https://portal.azure.us/) vez disso.

2. Introduza o **Ambiente de Trabalho Virtual do Windows** na barra de pesquisa e, em seguida, encontre e selecione o Windows Virtual **Desktop** em Serviços.

3. Na página de visão geral do **Ambiente de Trabalho Virtual do Windows,** selecione Criar uma piscina de **anfitriões**.

4. No **separador Básicos,** selecione a subscrição correta nos detalhes do Projeto.

5. Selecione **Criar novo** para criar um novo grupo de recursos ou selecione um grupo de recursos existente a partir do menu suspenso.

6. Insira um nome único para a sua piscina de anfitriões.

7. No campo Localização, selecione a região onde pretende criar a piscina anfitriã a partir do menu suspenso.

   A geografia Azure associada às regiões selecionadas é onde os metadados desta piscina anfitriã e seus objetos relacionados serão armazenados. Certifique-se de que escolhe as regiões dentro da geografia em que pretende que os metadados de serviço sejam armazenados.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do portal Azure mostrando o campo de Localização com a localização leste dos EUA selecionada. Ao lado do campo está o texto que diz: "Os metadados serão armazenados nos EUA Orientais."](media/portal-location-field.png)

8. No tipo de piscina host, selecione se a sua piscina de anfitriões será **Pessoal** ou **Piscina.**

    - Se escolher **o Personal,** selecione **automaticamente** ou **direto** no campo Tipo de Atribuição.

      > [!div class="mx-imgBorder"]
      > ![Uma imagem do menu drop-down do tipo de atribuição. O utilizador selecionou o Automatic.](media/assignment-type-field.png)

9.  Se escolher **Pooled, insira** as seguintes informações:

     - Para **o limite de sessão Max,** insira o número máximo de utilizadores que pretende equilibrar a carga para um único anfitrião de sessão.
     - Para **o algoritmo de equilíbrio de carga,** escolha a largura primeiro ou a profundidade primeiro, com base no seu padrão de utilização.

       > [!div class="mx-imgBorder"]
       > ![Uma imagem do campo do tipo de atribuição com "Pooled" selecionado. O Utilizador está a pairar sobre o seu cursor sobre a Amplitude-primeiro no menu de equilíbrio de carga.](media/pooled-assignment-type.png)

10. Selecione **Seguinte: Máquinas virtuais >**.

11. Se já criou máquinas virtuais e quer usá-las com o novo pool de anfitriões, selecione **Nº**, selecione **Next: Workspace >** e salte para a secção de informação do [Espaço de Trabalho.](#workspace-information) Se pretender criar novas máquinas virtuais e registá-las na nova piscina de anfitriões, selecione **Sim**.

Agora que completaste a primeira parte, vamos passar à próxima parte do processo de configuração onde criamos o VM.

## <a name="virtual-machine-details"></a>Detalhes da máquina virtual

Agora que já passamos a primeira parte, terá que preparar o seu VM.

Para configurar a sua máquina virtual dentro do processo de configuração da piscina do anfitrião:

1. No **grupo De Recursos,** escolha o grupo de recursos onde pretende criar as máquinas virtuais. Este pode ser um grupo de recursos diferente daquele que você usou para a piscina de anfitrião.

2. Escolha o **local da máquina Virtual** onde pretende criar as máquinas virtuais. Podem ser iguais ou diferentes da região que selecionou para a piscina anfitriã.

3. Em seguida, escolha o **tamanho da máquina Virtual** que pretende utilizar. Pode manter o tamanho padrão como está ou selecionar alterar o **tamanho** para alterar o tamanho. Se selecionar Alterar o **tamanho,** na janela que aparece, escolha o tamanho da máquina virtual adequada para a sua carga de trabalho.

4. Em **Número de VMs,** forneça o número de VMs que pretende criar para a sua piscina de anfitriões.

    >[!NOTE]
    >O processo de configuração pode criar até 400 VMs enquanto configura o seu pool de anfitriões, e cada processo de configuração VM cria quatro objetos no seu grupo de recursos. Uma vez que o processo de criação não verifica a sua quota de subscrição, certifique-se de que o número de VMs que inseri está dentro dos limites Azure VM e API para o seu grupo de recursos e subscrição. Pode adicionar mais VMs depois de terminar de criar a sua piscina de anfitriões.

5. Depois disso, forneça um **prefixo nome** para nomear as máquinas virtuais que o processo de configuração cria. O sufixo será `-` com números a partir de 0.

6. Em seguida, escolha a imagem que precisa ser usada para criar a máquina virtual. Pode escolher a **Galeria** ou **a Mancha de Armazenamento.**

    - Se escolher **a Galeria,** selecione uma das imagens recomendadas do menu suspenso:

      - Windows 10 Enterprise multi-sessão, Versão 1909
      - Windows 10 Enterprise multi-sessão, Versão 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multi-sessão, Versão 2004
      - Windows 10 Enterprise multi-sessão, Versão 2004 + Microsoft 365 Apps

     Se não vir a imagem desejada, **selecione Procurar todas as imagens e discos**, o que lhe permite selecionar outra imagem na sua galeria ou uma imagem fornecida pela Microsoft e outras editoras. Certifique-se de que a imagem que escolher é uma das [imagens de SO suportadas](overview.md#supported-virtual-machine-os-images).

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do Marketplace com uma lista de imagens da Microsoft exibidas.](media/marketplace-images.png)

     Também pode ir a **My Items** e escolher uma imagem personalizada que já carregou.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem do separador My Items.](media/my-items.png)

    - Se escolher **o Storage Blob,** pode aproveitar a sua própria imagem através do Hyper-V ou num VM Azure. Tudo o que tem de fazer é introduzir a imagem na bolha de armazenamento como um URI.

7. Escolha que tipo de discos de SO pretende que os seus VMs utilizem: SSD Standard, Premium SSD ou HDD Standard.

8. Em Rede e segurança, selecione a **rede Virtual** e **a Sub-rede** onde pretende colocar as máquinas virtuais que cria. Certifique-se de que a rede virtual pode ligar-se ao controlador de domínio, uma vez que terá de juntar as máquinas virtuais dentro da rede virtual ao domínio. Os servidores DNS da rede virtual selecionada devem ser configurados para utilizar o IP do controlador de domínio.

9. Em seguida, selecione se deseja um IP público para as máquinas virtuais. Recomendamos que selecione **Não** porque um IP privado é mais seguro.

10. Selecione que tipo de grupo de segurança deseja: **Básico,** **Avançado** ou **Nenhum**.

    Se selecionar **Basic,** terá de selecionar se deseja uma porta de entrada aberta. Se selecionar **Sim,** escolha entre a lista de portas padrão para permitir ligações de entrada.

    >[!NOTE]
    >Para maior segurança, recomendamos que não abra portos públicos de entrada.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página do grupo de segurança que mostra uma lista de portas disponíveis num menu suspenso.](media/available-ports.png)

    Se escolher **Advanced,** selecione um grupo de segurança de rede existente que já configura.

11. Depois disso, selecione se deseja que as máquinas virtuais sejam unidas a um domínio específico e a uma unidade organizacional. Se escolher **Sim,** especifique o domínio a aderir. Pode opcionalmente adicionar uma unidade organizacional específica onde pretende que as máquinas virtuais estejam. Se escolher **Não,** os VMs serão unidos ao domínio correspondente ao sufixo do **domínio AD unem-se à UPN**.

  - Ao especificar um OU, certifique-se de que utiliza todo o caminho (Nome Distinto) e sem aspas.

12. Na conta de Administrador, insira as credenciais para o administrador de domínio do diretório ativo da rede virtual selecionada. Esta conta não pode ter a autenticação multi-factored (MFA) ativada. Ao aderir a um domínio Azure Ative Directory Domain Services (Azure AD DS), a conta deve fazer parte do grupo de administradores AD DC Azure e a palavra-passe da conta deve funcionar em Azure AD DS.

13. Selecione **Seguinte: Workspace >**.

Com isso, estamos prontos para iniciar a próxima fase de configuração do seu pool de anfitriões: registar o seu grupo de aplicações num espaço de trabalho.

## <a name="workspace-information"></a>Informações da área de trabalho

O processo de configuração do pool anfitrião cria um grupo de aplicação de ambiente de trabalho por predefinição. Para que o pool do anfitrião funcione como pretendido, terá de publicar este grupo de aplicações para utilizadores ou grupos de utilizadores, e tem de registar o grupo de aplicações num espaço de trabalho.

Para registar o grupo de aplicações de desktop num espaço de trabalho:

1. Selecione **Yes** (Sim).

   Se selecionar **No**, pode registar o grupo de aplicações mais tarde, mas recomendamos que faça o registo do espaço de trabalho o mais rapidamente possível para que a sua piscina de anfitrião funcione corretamente.

2. Em seguida, escolha se deseja criar um novo espaço de trabalho ou selecionar a partir de espaços de trabalho existentes. Apenas espaços de trabalho criados no mesmo local onde a piscina anfitriã serão autorizados a registar o grupo de aplicações para.

3. Opcionalmente, pode selecionar **Seguinte: Tags >**.

    Aqui pode adicionar tags para que possa agrupar os objetos com metadados para facilitar as coisas para os seus administradores.

4. Quando terminar, selecione **Review + create**.

     >[!NOTE]
     >O processo de avaliação + criar não verifica se a sua palavra-passe cumpre os padrões de segurança ou se a sua arquitetura está correta, por isso terá de verificar se existe algum problema com qualquer uma dessas coisas por si mesmo.

5. Reveja as informações sobre a sua implementação para se certificar de que tudo parece estar certo. Quando concluir, selecione **Criar**. Isto inicia o processo de implantação, que cria os seguintes objetos:

     - Sua nova piscina de anfitriões.
     - Um grupo de aplicativos de desktop.
     - Um espaço de trabalho, se escolher criá-lo.
     - Se optar por registar o grupo de aplicações para desktop, o registo será concluído.
     - Máquinas virtuais, se optarem por criá-las, que se juntam ao domínio e registadas na nova piscina de anfitriões.
     - Um link de descarregamento para um modelo de Gestão de Recursos Azure baseado na sua configuração.

Depois disso, já acabou!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Executar o modelo de Gestor de Recursos Azure para providenciar uma nova piscina de anfitriões

Se preferir usar um processo automatizado, [baixe o nosso modelo de Gestor de Recursos Azure](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) para providenciar a sua nova piscina de anfitriões.

>[!NOTE]
>Se estiver a utilizar um processo automatizado para construir o seu ambiente, precisará da versão mais recente do ficheiro JSON de configuração. Pode encontrar o ficheiro JSON [aqui.](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)

## <a name="next-steps"></a>Próximos passos

Agora que fez a sua piscina de anfitriões, pode povoá-la com programas RemoteApp. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, dirija-se ao nosso próximo tutorial:

> [!div class="nextstepaction"]
> [Gerir grupos de aplicações tutorial](./manage-app-groups.md)
