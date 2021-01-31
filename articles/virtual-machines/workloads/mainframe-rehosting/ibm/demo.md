---
title: Crie uma Distribuição Controlada de Desenvolvedores de Aplicações (ADCD) em&de | de | Microsoft Docs
description: Executar um ambiente de desenvolvimento e teste IBM Z (zD&T) em Azure Virtual Machines (VMs).
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: c76768fa94b524763ac8ad94c9c3f5bb3a73dddb
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219443"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Configurar uma Distribuição Controlada de Desenvolvedores de Aplicações (ADCD) em IBM zD&T v1

Você pode executar um ambiente ibm Z desenvolvimento e ambiente de teste (zD&T) em Máquinas Virtuais Azure (VMs). Este ambiente imita a arquitetura da Série IBM Z. Pode hospedar uma variedade de sistemas operativos ou instalações da Série Z (também chamadas de Z Instances ou Pacotes), que são disponibilizadas através de pacotes personalizados chamados IBM Application Developers Controlled Distributions (ADCDs).

Este artigo mostra-lhe como configurar um caso ADCD num ambiente zD&T em Azure. Os ADCDs criam implementações completas do sistema operativo Série Z para ambientes de desenvolvimento e teste que funcionam em zD&T.

Tal como o zD&T, os ADCDs estão disponíveis apenas para clientes e parceiros da IBM e são exclusivamente para fins de desenvolvimento e teste. Não devem ser utilizados em ambientes de produção. Numerosos pacotes de instalação IBM estão disponíveis para download através [de Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou IBM [PartnerWorld.](https://www.ibm.com/partnerworld/public)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [ambiente zD&T][ibm-install-z] previamente estabelecido no Azure. Este artigo pressupõe que está a usar a mesma imagem Ubuntu 16.04 VM criada anteriormente.

- Acesso aos meios ADCD através do IBM PartnerWorld ou da Vantagem de Passaporte.

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isto é necessário para executar IBM zD&T. A forma como o cria depende da forma como licencia o software da IBM:

  - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para aceder a todas as partes do software. Tem de o obter da IBM.

  - **O servidor de licenciamento baseado em software** requer que você crie um servidor centralizado para a gestão das chaves de licenciamento. Este método é preferido e requer que você configura as chaves que recebe da IBM no servidor de gestão.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Descarregue os pacotes de instalação a partir da Vantagem passport

É necessário aceder aos meios de comunicação ADCD. Os passos abaixo assumem que você é um cliente IBM e pode usar a Vantagem do Passaporte. Os parceiros da IBM podem utilizar [o IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

> [!NOTE]
> Este artigo pressupõe que um Pc Windows é utilizado para aceder ao portal Azure e para descarregar os meios de comunicação ibm. Se estiver a utilizar um ambiente de trabalho Mac ou Ubuntu, os comandos e processos para obter os meios IBM podem diferir ligeiramente.

1. Inicie sessão na [Vantagem do Passaporte.](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)

2. Selecione **Downloads de Software** e **Acesso a Mídia**.

3. Selecione **o número de oferta de programa e de acordo**, e clique em **Continuar**.

4. Introduza a descrição da peça ou o número da peça e clique em **Finder**.

5. Opcionalmente, clique na lista de encomendas alfabéticas para visualizar e ver o produto pelo nome.

6. Selecione **todos os Sistemas Operativos** no **campo do sistema operativo** e em todos os **idiomas** no **campo idiomas**. Em seguida, clique em **Go**.

7. Clique **em Selecionar ficheiros individuais** para expandir a lista e exibir os meios individuais para descarregar.

8. Verifique os pacotes que pretende descarregar, selecione **Download** e, em seguida, descarregue os ficheiros para o diretório que deseja.

## <a name="upload-the-adcd-packages"></a>Faça o upload do(s) pacote(s) ADCD

Agora que tem os pacotes, deve enviá-los para o seu VM em Azure.

1. No portal Azure, inicie uma sessão **de ssh** com o VM Ubuntu que criou. Vá ao seu VM, selecione a lâmina **de visão geral** e, em seguida, selecione **Connect**.

2. Selecione o separador **SSH** e, em seguida, copie o comando ssh para a área de transferência.

3. Inicie sessão no seu VM utilizando as suas credenciais e o [cliente SSH](../../../linux/use-remote-desktop.md) de eleição. Esta demonstração utiliza as extensões Linux para o Windows 10, que adiciona uma casca de pancada ao pedido de comando do Windows. Putty também funciona.

4. Quando iniciar sessão, crie um diretório para carregar os pacotes IBM. Lembre-se que Linux é sensível a casos. Por exemplo, esta demonstração pressupõe que os pacotes são enviados para:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Faça o upload dos ficheiros utilizando um cliente SSH, como[o WinSCP.](https://winscp.net/eng/index.php) Uma vez que a SCP faz parte da SSH, utiliza a porta 22, que é o que o SSH usa. Se o seu computador local não for o Windows, pode digitar o [comando SCP](http://man7.org/linux/man-pages/man1/scp.1.html) na sua sessão SSH.

6. Inicie o upload para o diretório Azure VM que criou, que se torna o armazenamento de imagem para zD&T.

    > [!NOTE]
    > Certifique-se de que **ADCDTOOLS.XML** está incluído no upload para o **diretório home/MyUserID/ZDT/adcd/nov2017.** Precisará dele mais tarde.

7. Aguarde que os ficheiros sejam carregados, o que pode demorar algum tempo, dependendo da sua ligação ao Azure.

8. Quando os uploads estiverem completos, navegue para o diretório de volumes e descomprimir todos os volumes **gz:**

    ```console
    gunzip \*.gz
    ```
    
![Explorador de ficheiros mostrando volumes de gz descomprimidos](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configure o armazenamento de imagem

O próximo passo é configurar o zD&T para utilizar o(s) pacote(s) carregado. O processo de armazenamento de imagem dentro do zD&T permite-lhe montar e utilizar as imagens. Pode utilizar SSH ou FTP.

1. Inicie o **zDTServer**. Para isso, tens de estar ao nível das raízes. Introduza os dois seguintes comandos por ordem:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Note a saída de URL pelo comando e use este URL para aceder ao servidor web. Parece-se com:
     > https://(o seu nome VM ou endereço IP):9443/ZDTMC/index.html
     >
     > Lembre-se, o seu acesso à web utiliza a porta 9443. Utilize isto para iniciar sessão no servidor web. O ID do utilizador para ZD&T é **zdtadmin** e a palavra-passe é **senha**.

    ![IBM zD&T Enterprise Edition Ecrã de boas-vindas](media/02-welcome.png)

3. Na página **'Início Rápido',** em **Configuração,** selecione **Image Storage**.

     ![IBM zD&T Enterprise Edition Quick Start screen](media/03-quickstart.png)

4. Na página **de armazenamento de imagem configurar,** selecione O Protocolo de Transferência de **Ficheiros SSH**.

5. Para **o nome do anfitrião,** **digite Localhost** e introduza o percurso do diretório para onde carregou as imagens. Por exemplo, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Introduza o **ID do utilizador** e **a palavra-passe** para o VM. Não utilize o ID do utilizador&ZD E a palavra-passe.

7. Teste a ligação para se certificar de que tem acesso e, em seguida, **selecione Guardar** para guardar a configuração.

## <a name="configure-the-target-environments"></a>Configurar os ambientes-alvo

O próximo passo é configurar o ambiente alvo do zD&T. Este ambiente emulado é onde as suas imagens são executadas.

1. Na página **'Início Rápido',** em **Configuração,** selecione **Ambientes-alvo**.

2. Na página **de ambientes-alvo configurar,** selecione **Add Target**.

3. Selecione **Linux**. A IBM suporta dois tipos de ambientes, Linux e Cloud (OpenStack), mas esta demo funciona no Linux.

4. Na página **de ambiente de destino Adicionar,** para o nome do **anfitrião,** insira **localmente**. Mantenha a **porta SSH** definida para **22**.

5. Na caixa de **etiquetas Target Environment,** introduza uma etiqueta como **o MyCICS.**

     ![Adicione o ecrã do ambiente alvo](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configure a ADCD e implemente

Depois de completar os passos de configuração anteriores, deve configurar o zD&T para utilizar o(s) embalagem e o ambiente alvo. Mais uma vez, utiliza o processo de armazenamento de imagem em ZD&T, que lhe permite montar e usar as imagens. Pode utilizar SSH ou FTP.

1. Na página **'Início Rápido',** em **Configuração,** selecione **ADCD**. Aparece um conjunto de instruções, indicando-lhe os passos que precisam de ser preenchidos antes de uma embalagem ADCD poder ser montada. Isto explica porque nomeámos o diretório-alvo como fizemos antes.

2. Assumindo que todas as imagens foram enviadas para os diretórios corretos, clique na imagem do link **ADCD** exibido no lado inferior direito (mostrado no passo 7 na imagem seguinte).

     ![IBM zD&T Enterprise Edition - Ecrã ADCD configurado](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando o passo de configuração anterior estiver concluído, aparece a imagem criar uma imagem utilizando a página **de Componentes ADCD.**

1. Selecione o volume (nov 2017 neste caso) para exibir os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione **o Sistema de Controlo de Informações do Cliente (CICS) - 5.3**.

3. Na caixa de **nome de imagem,** digite um nome para a imagem como **MyCICS Image**.

4. Selecione o botão **Criar Imagem** no lado inferior direito.

     ![IBM zD&T Enterprise Edition - Criar uma imagem usando ecrã de componentes ADCD](media/06-adcd.png)

5. Na janela que aparece, dizendo-lhe que a imagem foi implementada com sucesso, escolha **implementar imagens**.

6. Na **imagem de implementar uma imagem para uma** página de ambiente alvo, selecione a imagem que criou na página anterior **(Imagem MyCICS)** e o ambiente-alvo criado anteriormente **(MyCICS).**

7. No ecrã seguinte, forneça as suas credenciais para o VM (isto é, não para a credencial ztadmin).

8. No painel de propriedades, insira o número de **processadores Centrais (CPs),** a quantidade de **memória do Sistema (GB)** e o **diretório de implantação** para a imagem de execução. Já que isto é uma demonstração, mantenha-a pequena.

9. Certifique-se de que a caixa está selecionada para **emitir automaticamente o comando IPL para z/OS após a sua implantação**.

     ![Tela de propriedades](media/07-properties.png)

10. Selecione **Complete**.

11. Selecione **Implementar imagem** a partir da imagem para uma página **de ambiente alvo.**

A sua imagem pode agora ser implantada e está pronta para ser montada por um emulador terminal 3270.

> [!NOTE]
> Se receber um erro dizendo que não tem espaço suficiente em disco, note que a região requer 151 Gb.

Parabéns! Está agora a gerir um ambiente central da IBM em Azure.

## <a name="learn-more"></a>Saber mais

- [Migração do quadro principal: mitos e factos](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale em Azure](ibm-db2-purescale-azure.md)
- [Resolução de problemas](../../../troubleshooting/index.yml)
- [Desmistificar o mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
