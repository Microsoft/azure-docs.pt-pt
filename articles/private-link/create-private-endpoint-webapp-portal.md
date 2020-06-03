---
title: Conecte-se privadamente a uma Aplicação Web usando o Azure Private Endpoint
description: Conecte-se privadamente a uma Aplicação Web usando o Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 06/02/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 34ea9961c257f398ebed3baaf4ed2ee5b6f3547e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298708"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Conecte-se privadamente a uma Aplicação Web utilizando o Azure Private Endpoint (Preview)

Azure Private Endpoint é o bloco de construção fundamental para private link em Azure. Permite-lhe ligar-se em privado à sua Web App.
Neste Quickstart, você vai aprender a implementar uma Web App com Private Endpoint e conectar-se a esta Aplicação Web a partir de uma Máquina Virtual.

Para obter mais informações, consulte [utilizar pontos finais privados para a Azure Web App][privatenedpointwebapp].

> [!Note]
>A pré-visualização está disponível em regiões públicas para aplicações Web PremiumV2 Windows e Linux e funções Elastic Premium. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Rede virtual e Máquina Virtual

Nesta secção, irá criar a rede virtual e a sub-rede para hospedar o VM que é utilizado para aceder à sua Web App através do Ponto Final Privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede.

1. No lado superior esquerdo do ecrã, selecione Criar uma rede virtual de rede de **Create a resource**  >  **recursos**ou procurar  >  **Virtual network** rede **Virtual** na caixa de pesquisa.

1. Na **Criação de rede virtual,** insira ou selecione esta informação no separador Básicos:

   > [!div class="mx-imgBorder"]
   > ![Criar Rede Virtual][1]

1. Clique **em "Seguinte: Endereços IP >"** e introduza ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Configurar endereços IP][2]

1. Na secção sub-rede, clique em **"+ Adicionar Subnet"** e introduza as seguintes informações e clique em **"Adicionar"**

   > [!div class="mx-imgBorder"]
   >![Adicionar Sub-rede][3]

1. Clique **em "Rever + criar"**

1. Após a validação passada, clique em **"Criar"**

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**  >  máquina virtual**compute de**  >  **Virtual machine** recurso

1. Em Criar uma máquina virtual - Básicos, insira ou selecione esta informação:

   > [!div class="mx-imgBorder"]
   >![Máquina virtual básica][4]

1. Selecione **"Seguinte: Discos"**

   Mantenha as definições predefinidos.

1. Selecione **"Seguinte: Networking",** selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Rede][5]

1. Clique **em "Review + Create"**

1. Quando a validação passou a mensagem, clique em **"Criar"**

## <a name="create-your-web-app-and-private-endpoint"></a>Crie a sua Web App e Private Endpoint

Nesta secção, irá criar uma Web App privada utilizando um Ponto Final Privado.

> [!Note]
>A função Private Endpoint só está disponível para o Premium V2 SKU.

### <a name="web-app"></a>Aplicação Web

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**  >  **Web**  >  **Web App** de recursos

1. In Create Web App - Básicos, insira ou selecione estas informações:

   > [!div class="mx-imgBorder"]
   >![Web App básico][6]

1. Selecione **"Review + create"**

1. Quando a validação passou a mensagem, clique em **"Criar"**

### <a name="create-the-private-endpoint"></a>Criar o ponto final privado

1. Nas propriedades da Web App, selecione **Definições**  >  **de Rede** e clique em **"Configure as suas ligações de ponto final privado"**

   > [!div class="mx-imgBorder"]
   >![Rede de aplicativos web][7]

1. No assistente, clique em **"+ adicionar"**

   > [!div class="mx-imgBorder"]
   >![Ponta de final privada da Web App][8]

1. Preencha as informações de subscrição, VNet e Sub-rede e clique em **"OK"**

   > [!div class="mx-imgBorder"]
   >![Rede de aplicativos web][9]

1. Reveja a criação do ponto final privado

   > [!div class="mx-imgBorder"]
   >![Rever ][10]
   > ![ visão final do ponto final privado][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

1. Na barra de pesquisa do portal, insira **o myVm**
1. Selecione o **botão Ligar**. Depois de selecionar o botão Ligar, ligue-se à máquina virtual, selecione **RDP**

   > [!div class="mx-imgBorder"]
   >![Botão RDP][12]

1. O Azure cria um ficheiro Remote Desktop Protocol (.rdp) e transfere-o para o seu computador depois de clicar no **ficheiro Download RDP**

   > [!div class="mx-imgBorder"]
   >![Baixar ficheiro RDP][13]

1. Abra o ficheiro.rdp descarregado.

- Se lhe for pedido, selecione Ligar.
- Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

> [!Note]
> Poderá ter de selecionar Mais opções > Utilizar uma conta diferente, para especificar as credenciais que introduziu quando criou o VM.

- Selecione OK.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione Sim ou Continue.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

## <a name="access-web-app-privately-from-the-vm"></a>Aceder à Web App em privado a partir do VM

Nesta secção, irá ligar-se em privado à Web App utilizando o Ponto Final Privado.

1. Obtenha o IP privado do seu Ponto Final Privado, no tipo de barra de pesquisa **Private Link,** e selecione Private Link

   > [!div class="mx-imgBorder"]
   >![Ligação Privada][14]

1. No Private Link Center, selecione **Private Endpoints** para listar todos os seus pontos de final privados

   > [!div class="mx-imgBorder"]
   >![Centro de Ligação Privada][15]

1. Selecione o link Private Endpoint para a sua Web App e a sua sub-rede

   > [!div class="mx-imgBorder"]
   >![Propriedades de ponto final privado][16]

1. Copie o IP Privado do seu Ponto Final Privado e o FQDN da sua Web App, no nosso caso webappdemope.azurewebsites.net 10.10.2.4

1. No myVM, verifique se a Web App não está acessível através do IP público. Abra um navegador e cole o nome da Web App, você deve ter uma página de erro 403 proibida

   > [!div class="mx-imgBorder"]
   >![Proibido][17]

> [!Important]
> Como esta funcionalidade está em pré-visualização, é necessário gerir manualmente a entrada DNS.

1. Crie a entrada do anfitrião, abra o explorador de ficheiros e localize o ficheiro dos anfitriões

   > [!div class="mx-imgBorder"]
   >![Arquivo de anfitriões][18]

1. Adicione uma entrada com o endereço IP privado e o nome público da sua App Web editando o ficheiro dos anfitriões com bloco de notas

   > [!div class="mx-imgBorder"]
   >![Conteúdo dos anfitriões][19]

1. Guarde o ficheiro

1. Abra um navegador e escreva o url da sua aplicação web

   > [!div class="mx-imgBorder"]
   >![Web site com PE][20]

1. Está a aceder à sua Aplicação Web através do Ponto Final Privado

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar o Ponto Final Privado, a Web App e o VM, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o pronto-rg na caixa de Busca na parte superior do portal e selecione ready-rg a partir dos resultados da pesquisa.
1. Selecione Eliminar grupo de recursos.
1. Introduza o pronto-rg para o TYPE O NOME DO GRUPO DE RECURSOS e selecione Delete.

## <a name="next-steps"></a>Passos seguintes

Neste Quickstart, criou um VM numa rede virtual, uma Web App e um Private Endpoint. Ligou-se a um VM da Internet e comunicou-se de forma segura à Web App utilizando o Private Link. Para saber mais sobre o Private Endpoint, consulte [o que é O Ponto Final Privado Azure.][privateendpoint]

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
