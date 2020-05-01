---
title: Windows Virtual Desktop host pool Azure portal - Azure
description: Como criar um pool de anfitriões do Windows Virtual Desktop utilizando o portal Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: def3ed840d2886aabfce1d1081c94298083fe6d6
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611664"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutorial: Criar uma piscina de acolhimento com o portal Azure

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas (VMs) dentro de ambientes de ambiente de trabalho virtual do Windows. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

Este artigo irá acompanhá-lo através do processo de configuração para criar um pool de anfitriões para um ambiente de ambiente de trabalho virtual Windows através do portal Azure. Este método fornece uma interface de utilizador baseada no navegador para criar um pool de anfitriões no Windows Virtual Desktop, criar um grupo de recursos com VMs numa subscrição Azure, juntar esses VMs ao domínio Azure Ative Directory (AD) e registar os VMs com Windows Virtual Desktop.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará introduzir os seguintes parâmetros para criar uma piscina de acolhimento:

- O nome da imagem VM
- Configuração VM
- Propriedades de domínio e rede
- Propriedades de piscina de hospedar ambiente de trabalho virtual windows

Também precisa saber as seguintes coisas:

- Onde está a fonte da imagem que pretende utilizar. É da Galeria Azure ou é uma imagem personalizada?
- O seu domínio junta-se a credenciais.

Ao criar um conjunto de anfitriões do Windows Virtual Desktop com o modelo Do Gestor de Recursos Azure, pode criar uma máquina virtual a partir da galeria Azure, uma imagem gerida ou uma imagem não gerida. Para saber mais sobre como criar imagens VM, consulte [Prepare um Windows VHD ou VHDX para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/capture-image-resource.md).

Se ainda não tiver uma subscrição Azure, certifique-se de [criar uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar a seguir estas instruções.

## <a name="begin-the-host-pool-setup-process"></a>Inicie o processo de configuração da piscina anfitriã

Para começar a criar a sua nova piscina anfitriã:

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Introduza o **Windows Virtual Desktop** na barra de pesquisa e, em seguida, encontre e selecione O Ambiente de Trabalho Virtual do **Windows** em Serviços.

3. Na página de visão geral do **Windows Virtual Desktop,** selecione **Criar uma piscina de anfitriões**.

4. No separador **Basics,** selecione a subscrição correta sob os detalhes do Projeto.

5. Selecione **Criar novos** para fazer um novo grupo de recursos ou selecione um grupo de recursos existente a partir do menu suspenso.

6. Insira um nome único para a sua piscina anfitriã.

7. No campo Localização, selecione a região onde pretende criar a piscina anfitriã a partir do menu suspenso.
   
   A geografia Azure associada às regiões selecionadas é onde serão armazenados os metadados para esta piscina hospedeira e seus objetos relacionados. Certifique-se de que escolhe as regiões dentro da geografia que pretende que os metadados do serviço sejam armazenados.

     ![Uma imagem do portal Azure mostrando o campo de localização com a localização dos EUA Oriental selecionada. Ao lado do campo está o texto que diz: "Os metadados serão armazenados no Leste dos EUA."](media/portal-location-field.png)

8. Sob o tipo de piscina anfitrião, selecione se a sua piscina anfitriã será **pessoal** ou **pooled**.

    - Se escolher **Personal,** selecione **automático** ou **direto** no campo Tipo de Atribuição.

      ![Uma imagem do menu de entrega do campo de atribuição. O utilizador selecionou o Automático.](media/assignment-type-field.png)

9. Se escolher **Pooled,** introduza as seguintes informações:

     - Para o limite de **sessão Max,** insira o número máximo de utilizadores que deseja equilibrar a carga para um único anfitrião de sessão.
     - Para o algoritmo de **equilíbrio de carga,** escolha a amplitude em primeiro lugar ou a profundidade em primeiro lugar, com base no seu padrão de utilização.

       ![Uma imagem do campo tipo de atribuição com "Pooled" selecionado. O Utilizador está a pairar sobre o seu cursor sobre a Amplitude primeiro no menu de reposição de carga.](media/pooled-assignment-type.png)

10. Selecione **Seguinte: Detalhes vm**.

11. Se já criou máquinas virtuais e quer usá-las com a nova piscina anfitriã, selecione **No**. Se quiser criar novas máquinas virtuais e registá-las na nova piscina de anfitriões, selecione **Sim**.

Agora que completou a primeira parte, vamos passar à próxima parte do processo de configuração onde criamos o VM.

## <a name="virtual-machine-details"></a>Detalhes da máquina virtual

Agora que terminámos a primeira parte, vais ter de preparar o teu VM.

Para configurar a sua máquina virtual dentro do processo de configuração do pool anfitrião:

1. No Grupo de Recursos, escolha o grupo de recursos onde pretende criar as máquinas virtuais. Este pode ser um grupo de recursos diferente daquele que usou para a piscina anfitriã.

2. Escolha a **região** virtual da máquina onde pretende criar as máquinas virtuais. Podem ser iguais ou diferentes da região que selecionou para a piscina anfitriã.

3. Em seguida, escolha o tamanho da máquina virtual que pretende criar. Pode manter o tamanho predefinido como está ou selecionar o **Tamanho de Alteração** para alterar o tamanho. Se selecionar **O Tamanho**de Mudança , na janela que aparece, escolha o tamanho da máquina virtual adequada para a sua carga de trabalho.

4. Em número de VMs, forneça o número de VMs que pretende criar para a sua piscina anfitriã.

    >[!NOTE]
    >O processo de configuração pode criar até 400 VMs enquanto configura o seu pool de hospedeiro, e cada processo de configuração VM cria quatro objetos no seu grupo de recursos. Uma vez que os proces de criação não verificam a sua quota de subscrição, certifique-se de que o número de VMs que introduz está dentro dos limites de VM e API Azure para o seu grupo de recursos e subscrição. Pode adicionar mais VMs depois de terminar de criar a sua piscina de anfitriões.

5. Depois disso, forneça um **prefixo de nome** para nomear as máquinas virtuais que o processo de configuração cria. O sufixo `-` será com números a partir de 0.

6. Em seguida, escolha a imagem que precisa de ser usada para criar a máquina virtual. Pode escolher **galeria** ou **Blob de Armazenamento.**

    - Se escolher **a Galeria,** selecione uma das imagens recomendadas no menu suspenso:

      - Windows 10 Enterprise multi-sessão, Versão 1909 + Office 365 ProPlus – Gen 1
      - Windows 10 Enterprise multi-sessão, Versão 1909 - Gen 1
      - Windows Server 2019 Datacenter - Gen1

     Se não vir a imagem que deseja, **selecione Navegar em todas as imagens e discos**, o que lhe permite selecionar outra imagem na sua galeria ou uma imagem fornecida pela Microsoft e outras editoras.

     ![Uma imagem do Marketplace com uma lista de imagens da Microsoft exibidas.](media/marketplace-images.png)

     Você também pode ir aos **Meus Itens** e escolher uma imagem personalizada que já carregou.

     ![Uma imagem do separador My Items.](media/my-items.png)

    - Se escolher **o Storage Blob,** pode alavancar a sua própria imagem através do Hyper-V ou num VM Azure. Tudo o que tem que fazer é inserir a localização da imagem na bolha de armazenamento como URI.

7. Escolha que tipo de discos OS deseja que os seus VMs utilizem: Standard SSD, Premium SSD ou Standard HDD.

8. Em Rede e segurança, selecione a rede virtual e a subnet onde pretende colocar as máquinas virtuais que cria. Certifique-se de que a rede virtual pode ligar-se ao controlador de domínio, uma vez que terá de se juntar às máquinas virtuais dentro da rede virtual ao domínio. Em seguida, selecione se quer ou não um IP público para as máquinas virtuais. Recomendamos que selecione **Não,** porque um IP privado é mais seguro.

9. Selecione que tipo de grupo de segurança deseja: **Básico,** **Avançado**ou **Nenhum**.

    Se selecionar **Basic,** terá de selecionar se pretende que seja aberta alguma porta de entrada. Se selecionar **Sim,** escolha entre a lista de portas padrão para permitir ligações de entrada.

    >[!NOTE]
    >Para maior segurança, recomendamos que não abra portos públicos de entrada.

    ![Uma imagem da página do grupo de segurança que mostra uma lista de portas disponíveis num menu suspenso.](media/available-ports.png)
    
    Se escolher **o Advanced**, selecione um grupo de segurança de rede existente que já configurado.

10. Depois disso, selecione se pretende que as máquinas virtuais sejam unidas a um domínio específico e unidade organizacional. Se escolher **Sim,** especifique o domínio para aderir. Também pode adicionar uma unidade organizacional específica onde deseja que as máquinas virtuais estejam.

11. Na conta Do Administrador, introduza as credenciais para o administrador do Domínio do Diretório Ativo da rede virtual selecionada.

12. Selecione **Workspace**.

Com isso, estamos prontos para iniciar a próxima fase de configuração do seu pool de anfitriões: registar o seu grupo de aplicações num espaço de trabalho.

## <a name="workspace-information"></a>Informações da área de trabalho

O processo de configuração do pool de anfitrião cria um grupo de aplicações de ambiente de trabalho por padrão. Para que o conjunto de anfitriões funcione como pretendido, terá de publicar este grupo de aplicações a utilizadores ou grupos de utilizadores, e tem de registar o grupo de aplicações num espaço de trabalho. 

Para registar o grupo de aplicações de ambiente de trabalho num espaço de trabalho:

1. Selecione **Sim**.

   Se selecionar **Não,** pode registar o grupo de aplicações mais tarde, mas recomendamos que faça o registo do espaço de trabalho o mais rápido possível para que a sua piscina de anfitriões funcione corretamente.

2. Em seguida, escolha se pretende criar um novo espaço de trabalho ou selecione a partir de espaços de trabalho existentes. Apenas espaços de trabalho criados no mesmo local que a piscina anfitriã será permitido registar o grupo de aplicações para.

3. Opcionalmente, pode selecionar **Tags**.

    Aqui pode adicionar tags para que possa agrupar os objetos com metadados para facilitar as coisas aos seus administradores.

4. Quando terminar, selecione **Review + crie**. 

     >[!NOTE]
     >A revisão + criar o processo de validação não verifica se a sua palavra-passe cumpre os padrões de segurança ou se a sua arquitetura está correta, por isso terá de verificar quaisquer problemas com qualquer uma dessas coisas. 

5. Reveja as informações sobre a sua implementação para se certificar de que tudo parece correto. Quando terminar, selecione **Criar**. Isto inicia o processo de implantação, que cria os seguintes objetos:

     - Sua nova piscina de anfitrião.
     - Um grupo de aplicativos de ambiente de trabalho.
     - Um espaço de trabalho, se escolheu criá-lo.
     - Se optou por registar o grupo de aplicações de desktop, o registo estará completo
     - Máquinas virtuais, se optar por criá-las, que se juntam ao domínio e registadas com a nova piscina anfitriã.
     - Um link de descarregamento para um modelo de Gestão de Recursos Azure baseado na sua configuração.

Depois disso, já acabaram!

## <a name="next-steps"></a>Passos seguintes

Agora que fez a sua piscina de anfitriões, pode povoá-la com programas RemoteApp. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, dirija-se ao nosso próximo tutorial:

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
