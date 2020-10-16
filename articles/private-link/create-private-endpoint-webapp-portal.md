---
title: Conecte-se privadamente a uma aplicação web utilizando o Azure Private Endpoint
description: Este artigo explica como ligar-se privadamente a uma aplicação web utilizando o Azure Private Endpoint.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772843"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Conecte-se privadamente a uma aplicação web utilizando o Azure Private Endpoint

Azure Private Endpoint é o bloco de construção fundamental para a Azure Private Link. Ao utilizar o Private Endpoint, pode ligar-se em privado à sua aplicação web. Neste artigo, você vai aprender a implementar uma aplicação web usando Private Endpoint e, em seguida, conectar-se à aplicação web a partir de uma máquina virtual (VM).

Para obter mais informações, consulte [utilizar pontos finais privados para uma aplicação web Azure][privateendpointwebapp].

> [!Note]
> O Private Endpoint está disponível em regiões públicas para aplicações web PremiumV2 de nível PremiumV3, aplicações web Do 43 ºC, aplicações web Linux e o plano Azure Functions Premium (por vezes referido como o plano Elastic Premium). 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Antes de começar, inscreva-se [no portal Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Criar uma rede virtual e máquina virtual

Nesta secção, cria-se uma rede virtual e uma sub-rede para hospedar um VM que utilizará para aceder a uma aplicação web através de um ponto final privado.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Para criar a rede virtual e a sub-rede, faça o seguinte:

1. No painel esquerdo, **selecione Criar uma**rede virtual  >  **de rede de**recursos  >  **Virtual network**.

1. No painel **de rede virtual Create,** selecione o **separador Básicos** e, em seguida, introduza a informação que é mostrada aqui:

   > [!div class="mx-imgBorder"]
   > ![Screenshot do painel "Create Virtual Network" no portal Azure.][1]

1. Selecione o separador **endereços IP** e, em seguida, introduza as informações mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Screenshot do separador "Endereços IP" no painel de rede virtual Create.][2]

1. Na secção **sub-rede,** **selecione Add Subnet,** introduza as informações mostradas aqui e, em seguida, selecione **Adicionar**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do painel "Adicionar sub-rede".][3]

1. Selecione **Rever + criar**.

1. Após validação bem sucedida, **selecione Criar**.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Para criar a máquina virtual, faça o seguinte:

1. No portal Azure, no painel esquerdo, selecione **Criar uma**máquina Virtual  >  **compute de**  >  **Virtual machine**recurso.

1. No **Create a virtual machine - Basics** pane, insira a informação que é mostrada aqui:

   > [!div class="mx-imgBorder"]
   > ![Screenshot do painel "Criar uma máquina virtual".][4]

1. Selecione **Seguinte: Discos**.

1. No painel **de discos,** mantenha as definições predefinidos e, em seguida, selecione **Seguinte: Networking**.

1. No painel **de networking,** insira a informação que é mostrada aqui:

   > [!div class="mx-imgBorder"]
   > ![Screenshot do separador "Networking" no painel "Criar uma máquina virtual".][5]

1. Selecione **Rever + criar**.

1. Após validação bem sucedida, **selecione Criar**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Crie uma aplicação web e um ponto final privado

Nesta secção, você cria uma aplicação web privada que usa um ponto final privado.

> [!Note]
> A função Private Endpoint está disponível apenas para o nível PremiumV2 e PremiumV3.

### <a name="create-the-web-app"></a>Criar a aplicação Web

1. No portal Azure, no painel esquerdo, selecione **Criar uma**Web App de recursos  >  **Web**  >  **Web App**.

1. No painel de **aplicações** web, selecione o separador **Básicos** e, em seguida, introduza as informações mostradas aqui:

   > [!div class="mx-imgBorder"]
   > ![Screenshot do separador "Básicos" no painel "Web App".][6]

1. Selecione **Rever + criar**.

1. Após validação bem sucedida, **selecione Criar**.

### <a name="create-the-private-endpoint"></a>Criar o ponto final privado

1. Nas propriedades de **aplicativos**web, em Definições , selecione **Networking**e, em seguida, em conexões **Private Endpoint **, selecione **Configure as suas ligações de ponto final privado**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do link "Configurar as suas ligações de ponto final privado" no painel de rede de aplicações web.][7]

1. No assistente **de ligações Private Endpoint,** selecione **Adicionar**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do botão Adicionar no assistente "Ligações Private Endpoint".][8]

1. Selecione as informações corretas nas listas de **subscrição,** **rede virtual**e **sub-rede** e, em seguida, selecione **OK**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do painel "Add Private Endpoint".][9]

1. Monitorize o progresso da criação de pontos finais privados.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do progresso da adição do ponto final privado. ][10]
   >  ![ Screenshot do recém-criado ponto final privado.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Ligue-se ao VM a partir da internet

1. Na caixa de **pesquisa** do portal Azure, insira **o myVm**.
1. Selecione **Connect**e, em seguida, selecione **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do botão "RDP" no painel "myVM".][12]

1. No **painel 'Ligar com RDP',** selecione **Download RDP file**.  

   > [!div class="mx-imgBorder"]
   > ![Screenshot do botão "Download RDP File" no painel "Connect with RDP".][13]

   O Azure cria um ficheiro Remote Desktop Protocol (RDP) e transfere-o para o seu computador.   

1. Abra o ficheiro RDP transferido.

   a. No momento, selecione **Connect**.  
   b. Introduza o nome de utilizador e a palavra-passe que especificou quando criou o VM.

     > [!Note]
     > Para utilizar estas credenciais, poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente.**

1. Selecione **OK**.

   > [!Note]
   > Se receber um aviso de certificado durante o processo de inscrição, selecione **Sim** ou **Continue**.

1. Quando a janela do ambiente de trabalho VM aparecer, minimize-a para voltar ao seu ambiente de trabalho local.

## <a name="access-the-web-app-privately-from-the-vm"></a>Aceda à aplicação web em privado a partir do VM

Nesta secção, conecta-se em privado à aplicação web utilizando o ponto final privado.

1. Para obter o IP privado do seu ponto final privado, na caixa **de Pesquisa,** digite **link privado** e, em seguida, na lista de resultados, selecione **Private Link**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do link "Private Link" na lista de resultados de pesquisa.][14]

1. No Private Link Center, no painel esquerdo, selecione **pontos finais privados** para exibir os seus pontos finais privados.

   > [!div class="mx-imgBorder"]
   > ![Screenshot da lista de pontos finais privados no Private Link Center.][15]

1. Selecione o ponto final privado que se liga à sua aplicação web e à sua sub-rede.

   > [!div class="mx-imgBorder"]
   > ![Screenshot do painel de propriedades para um ponto final privado.][16]

1. Copie o IP privado do seu ponto final privado e o nome de domínio totalmente qualificado (FQDN) da sua aplicação web. No exemplo anterior, o ID privado é *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. No **painel myVM,** verifique se a aplicação web está inacessível através do IP público. Para tal, abra um navegador e cole o nome da aplicação web. A página deve apresentar uma mensagem "Erro 403 - Proibido".

   > [!div class="mx-imgBorder"]
   > ![Screenshot de uma página de erro "Error 403 - Forbidden".][17]

   Para o DNS, faça qualquer um dos seguintes:
 
   - Utilize o serviço de zona privada Azure DNS.  

     a. Crie uma zona privada DNS chamada *`privatelink.azurewebsites.net`* , e depois ligue-a à rede virtual.  
     b. Crie os dois registos A (isto é, o nome da aplicação e o nome do Gestor de Controlo de Serviço [SCM] com o endereço IP do seu ponto final privado.  
     > [!div class="mx-imgBorder"]
     > ![Screenshot dos registos da zona privada do DNS.][21]  

   - Utilize o ficheiro de *anfitriões* do VM.  

     a. Crie a entrada dos anfitriões, abra o File Explorer e procure o ficheiro dos *anfitriões.*  
     > [!div class="mx-imgBorder"]
     > ![Screenshot mostrando o ficheiro dos anfitriões no Explorador de Ficheiros.][18]  
     b. Adicione uma entrada que contenha o endereço IP privado e o nome público da sua aplicação web editando o ficheiro dos *anfitriões* num editor de texto.  
     > [!div class="mx-imgBorder"]
     > ![Screenshot do texto do ficheiro dos anfitriões.][19]  
     c. Guarde o ficheiro.

1. Num browser, digite o URL da sua aplicação web.

   > [!div class="mx-imgBorder"]
   > ![Screenshot de um navegador exibindo uma aplicação web.][20]

Está agora a aceder à sua aplicação web através do ponto final privado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar o ponto final privado, a aplicação web e o VM, elimine o grupo de recursos e todos os recursos que contém.

1. No portal Azure, na caixa **'Procurar',** **introduza o pronto-rg**e, em seguida, selecione **ready-rg** na lista de resultados.

1. Selecione **Eliminar grupo de recursos**.

1. No **tipo de tipo de grupo de recursos,** introduza o **pronto-rg**e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um VM numa rede virtual, uma aplicação web e um ponto final privado. Ligou-se a um VM da internet e comunicou-se de forma segura à aplicação web utilizando o Private Link. 

Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?][privateendpoint]

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
