---
title: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
description: Ligue-se em privado a uma Aplicação Web usando o Ponto Final Privado do Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287820"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Ligue-se em privado a uma Aplicação Web utilizando o Ponto Final Privado do Azure (Pré-visualização)

Azure Private Endpoint é o bloco de construção fundamental para a Private Link em Azure. Permite-lhe ligar-se em privado à sua Web App.
Neste Quickstart, você aprenderá a implementar uma Web App com Private Endpoint e conectar-se a esta Web App a partir de uma Máquina Virtual.

Para mais informações, consulte [A utilização de pontos finais privados para a Web App Azure][privatenedpointwebapp].

> [!Note]
>A pré-visualização está disponível nas regiões dos EUA Orientais e OESTE 2 para todas as Aplicações Web PremiumV2 Windows e Linux. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rede virtual e Máquina Virtual

Nesta secção, irá criar a rede virtual e a subnet para acolher o VM que é usado para aceder à sua Web App através do Private Endpoint.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, criará uma rede virtual e uma sub-rede.

1. No lado superior esquerdo do ecrã, selecione **Criar uma** > **rede virtual** de**rede** > de recursos ou procurar a **rede Virtual** na caixa de pesquisa.

1. Na **Create rede virtual,** introduza ou selecione estas informações no separador Basics:

   > [!div class="mx-imgBorder"]
   > ![Criar rede virtual][1]

1. Clique em **"Next: IP Addresss >"** e introduza ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Configure endereços IP][2]

1. Na secção de sub-rede, clique em **"+ Adicionar Subnet"** e introduza as seguintes informações e clique em **"Adicionar"**

   > [!div class="mx-imgBorder"]
   >![Adicionar Subnet][3]

1. Clique em **"Rever + criar"**

1. Depois da validação ter passado, clique em **"Criar"**

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** > máquina**Compute** > **Virtual** de recurso

1. Em Criar uma máquina virtual - Básicos, insira ou selecione esta informação:

   > [!div class="mx-imgBorder"]
   >![Máquina Virtual básica][4]

1. Selecione **"Next: Disks"**

   Mantenha as definições predefinidas.

1. Selecione **"Seguinte: Networking",** selecione esta informação:

   > [!div class="mx-imgBorder"]
   >![Rede][5]

1. Clique em **"Rever + Criar"**

1. Quando a validação passou a mensagem, clique em **"Criar"**

## <a name="create-your-web-app-and-private-endpoint"></a>Crie a sua Web App e endpoint privado

Nesta secção, irá criar uma Web App privada utilizando um Ponto Final Privado para a si.

> [!Note]
>A funcionalidade Private Endpoint só está disponível para o Premium V2 SKU.

### <a name="web-app"></a>Aplicação Web

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** > **Aplicação** **Web** > de recursos

1. In Create Web App - Basics, insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Web App básico][6]

1. Selecione **"Rever + criar"**

1. Quando a validação passou a mensagem, clique em **"Criar"**

### <a name="create-the-private-endpoint"></a>Criar o ponto final privado

1. Nas propriedades da Web App, selecione **Definições** > **de Rede** e clique em **"Configure as ligações de ponto final privado"**

   > [!div class="mx-imgBorder"]
   >![Rede web web][7]

1. No assistente, clique em **"+ adicionar"**

   > [!div class="mx-imgBorder"]
   >![Ponto final privado da aplicação web][8]

1. Preencha as informações de subscrição, VNet e Subnet e clique em **"OK"**

   > [!div class="mx-imgBorder"]
   >![Rede de aplicações web][9]

1. Rever a criação do ponto final privado

   > [!div class="mx-imgBorder"]
   >![Rever][10]
   >![a visão final do ponto final privado][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

1. Na barra de pesquisa do portal, entre no **myVm**
1. Selecione o **botão Ligar**. Depois de selecionar o botão Ligar, ligar **RDP** à máquina virtual abre- se

   > [!div class="mx-imgBorder"]
   >![Botão RDP][12]

1. O Azure cria um ficheiro Remote Desktop Protocol (.rdp) e transfere-o para o seu computador depois de clicar no **ficheiro RDP de download**

   > [!div class="mx-imgBorder"]
   >![Baixar ficheiro RDP][13]

1. Abra o ficheiro download.rdp.

- Se lhe for pedido, selecione Ligar.
- Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

> [!Note]
> Poderá ter de selecionar Mais opções > Utilize uma conta diferente, para especificar as credenciais que inseriu quando criou o VM.

- Selecione OK.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione Sim ou Continue.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

## <a name="access-web-app-privately-from-the-vm"></a>Aceder à Web App em privado a partir do VM

Nesta secção, irá ligar-se em privado à Web App utilizando o Ponto Final Privado.

1. Obtenha o IP privado do seu Ponto Final Privado, no tipo de barra de pesquisa **Private Link,** e selecione Private Link

   > [!div class="mx-imgBorder"]
   >![Ligação Privada][14]

1. No Private Link Center, selecione **Private Endpoints** para listar todos os seus pontos finais privados

   > [!div class="mx-imgBorder"]
   >![Centro de Ligação Privada][15]

1. Selecione o link Private Endpoint para a sua Web App e a sua subnet

   > [!div class="mx-imgBorder"]
   >![Propriedades de ponto final privado][16]

1. Copie o IP Privado do seu Ponto Final Privado e o FQDN da sua Web App, no nosso caso webappdemope.azurewebsites.net 10.10.2.4

1. No myVM, verifique se a Web App não está acessível através do IP público. Abra um navegador e reexa o nome da Web App, você deve ter uma página de erro proibida 403

   > [!div class="mx-imgBorder"]
   >![Proibido][17]

> [!Important]
> Como esta funcionalidade está em pré-visualização, é necessário gerir manualmente a entrada de DNS.

1. Crie a entrada do anfitrião, abra o explorador de ficheiros e localize o ficheiro dos anfitriões

   > [!div class="mx-imgBorder"]
   >![Arquivo de anfitriões][18]

1. Adicione uma entrada com o endereço IP privado e o nome público da sua Web App editando o ficheiro dos anfitriões com bloco de notas

   > [!div class="mx-imgBorder"]
   >![Hospedar conteúdo][19]

1. Guarde o ficheiro

1. Abra um navegador e escreva o url da sua aplicação web

   > [!div class="mx-imgBorder"]
   >![Web site com PE][20]

1. Você está a aceder à sua Web App através do Private Endpoint

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar usando o Private Endpoint, Web App e o VM, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o pronto-rg na caixa de pesquisa na parte superior do portal e selecione ready-rg a partir dos resultados da pesquisa.
1. Selecione Eliminar grupo de recursos.
1. Introduza o pronto-rg para ESCREVER O NOME DE GRUPO DE RECURSOS e selecione Eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste Quickstart, criou um VM numa rede virtual, numa Web App e num Private Endpoint. Ligou-se a um VM da Internet e comunicou-se de forma segura à Aplicação Web utilizando o Private Link. Para saber mais sobre private endpoint, veja [O que é Azure Private Endpoint][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
