---
title: Tutorial – Criar um registo de alias do DNS do Azure para suportar os nomes de domínio apex com o Gestor de Tráfego
description: Este tutorial mostra como pode configurar um registo de alias do DNS do Azure para suportar os nomes de domínio apex com o Gestor de Tráfego.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 6bb3506e60894db525efaf2985dd92f9eaaf9e0a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530965"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Tutorial: Configurar um registo de alias para oferecer suporte a nomes de domínio com o Gestor de tráfego do vértice 

Pode criar um registo de alias para o apex do seu nome de domínio para referenciar um perfil do Gestor de Tráfego do Azure. Um exemplo é contoso.com. Em vez de utilizar um serviço de redirecionamento, configure o DNS do Azure para referenciar um perfil do Gestor de Tráfego diretamente da sua zona. 


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM anfitrião e uma infraestrutura de rede.
> * Criar um perfil do Gestor de Tráfego.
> * Criar um registo de alias.
> * Testar o registo de alias.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções sobre como alojar o seu domínio no DNS do Azure, consulte [Tutorial: Aloje o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo utilizado para este tutorial é o contoso.com, mas utilize o seu próprio nome de domínio.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede
Primeiro, crie uma rede virtual e uma sub-rede onde possa colocar os servidores Web.
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. No canto superior esquerdo do portal, selecione **Criar um recurso**. Introduza *grupo de recursos* na caixa de pesquisa e crie um grupo de recursos chamado **RG-DNS-Alias-TM**.
3. Selecione **Criar um recurso** > **Rede** > **Rede virtual**.
4. Criar uma rede virtual denominada **VNet-Servers**. Coloque-o no grupo de recursos **RG-DNS-Alias-TM** e atribua à sub-rede o nome **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Criar duas máquinas virtuais do servidor Web
1. Selecione **Criar um recurso** > **VM do Windows Server 2016**.
2. Introduza **Web-01** para o nome e coloque a VM no grupo de recursos **RG-DNS-Alias-TM**. Introduza um nome de utilizador e uma palavra-passe e selecione **OK**.
3. Para **Tamanho**, selecione um SKU com 8 GB de RAM.
4. Em **Definições**, selecione a rede virtual **VNet-Servers** e a sub-rede **SN-Web**.
5. Selecione **Endereço IP público**. Sob **Atribuição**, selecione **Estático** e, em seguida, selecione **OK**.
6. Nas portas de entrada públicas, selecione **HTTP** > **HTTPS** > **RDP (3389)**, e, em seguida, selecione **OK**.
7. Na página **Resumo**, selecione **Criar**. Este procedimento demora alguns minutos a concluir.

Repita este procedimento para criar outra máquina virtual com o nome **Web-02**.

### <a name="add-a-dns-label"></a>Adicionar uma etiqueta de DNS
Os endereços IP públicos precisam de uma etiqueta de DNS para funcionar com o Gestor de Tráfego.
1. No grupo de recursos **RG-DNS-Alias-TM**, selecione o endereço IP público **Web-01-ip**.
2. Em **Definições**, selecione **Configuração**.
3. Na caixa de texto da etiqueta do nome DNS, introduza **web01pip**.
4. Selecione **Guardar**.

Repita este procedimento para o endereço IP público **Web-02-ip**, com **web02pip** para a etiqueta do nome DNS.

### <a name="install-iis"></a>Instalar o IIS

Instale o IIS em **Web-01** e **Web-02**.

1. Ligue-se a **Web-01** e inicie sessão.
2. No dashboard do **Gestor de Servidor**, selecione **Adicionar funções e funcionalidades**.
3. Selecione **Seguinte** três vezes. Na página **Funções de Servidor**, selecione **Servidor Web (IIS)**.
4. Selecione **Adicionar Funcionalidades** e selecione **Seguinte**.
5. Selecione **Seguinte** quatro vezes. Em seguida, selecione **Instalar**. Este procedimento demora alguns minutos a concluir.
6. Quando a instalação terminar, selecione **Fechar**.
7. Abra um browser. Navegue até **localhost** para verificar se é apresentada a página Web predefinida do IIS.

Repita este procedimento para instalar o IIS em **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

1. Abra o grupo de recursos **RG-DNS-Alias-TM** e selecione o Endereço IP público **Web-01-ip**. Aponte o endereço IP para utilizar mais tarde. Repita este passo para o endereço IP público **Web-02-ip**.
1. Selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego**.
2. Para o nome, introduza **TM-alias-test**. Coloque-o no grupo de recursos **RG-DNS-Alias-TM**.
3. Selecione **Criar**.
4. Após a implementação terminar, selecione **Ir para recurso**.
5. Na página de perfil do Gestor de Tráfego, em **Definições**, selecione **Pontos finais**.
6. Selecione **Adicionar**.
7. Em **Tipo**, selecione **Ponto final externo** e para **Nome**, introduza **EP-Web01**.
8. Na caixa de texto **Nome de domínio completamente qualificado (FQDN) ou IP**, introduza o endereço IP de **Web-01-ip** que apontou anteriormente.
9. Selecione a mesma **Localização** dos outros recursos e selecione **OK**.

Repita este procedimento para adicionar o ponto final **Web-02**, com o endereço IP que apontou anteriormente para **Web-02-ip**.

## <a name="create-an-alias-record"></a>Criar um registo de alias

Crie um registo de alias que esteja associado ao perfil do Gestor de Tráfego.

1. Selecione na sua zona DNS do Azure para abrir a zona.
2. Selecione **Conjunto de registos**.
3. Deixe a caixa de texto **Nome** em branco para representar o apex do nome de domínio. Um exemplo é contoso.com.
4. Deixe o **Tipo** como um registo **A**.
5. Selecione a caixa de verificação **Conjunto de Registos de Alias**.
6. Selecione **Escolher serviço do Azure** e selecione o perfil do Gestor de Tráfego **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testar o registo de alias

1. Num browser, navegue até ao apex do nome de domínio. Um exemplo é contoso.com. Deve ver a página Web predefinida do IIS. Feche o browser.
2. Encerre a máquina virtual **Web-01**. Aguarde alguns minutos até encerrar completamente.
3. Abra um browser e navegue novamente até ao apex do seu nome de domínio.
4. Verá novamente a página predefinida do IIS, uma vez que o Gestor de Tráfego resolveu a situação e direcionou o tráfego para **Web-02**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados para este tutorial, elimine o grupo de recursos **RG-DNS-Alias-TM**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um registo de alias para utilizar o seu nome de domínio do apex para referenciar um perfil do Gestor de Tráfego. Para saber mais sobre o DNS do Azure e as aplicações Web, avance para o tutorial de aplicações Web.

> [!div class="nextstepaction"]
> [Alojar aplicações Web com balanceamento de carga no apex da zona](./dns-alias-appservice.md)
