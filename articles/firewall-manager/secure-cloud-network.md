---
title: 'Tutorial: usar a visualização do Gerenciador de firewall do Azure para proteger sua rede de nuvem usando o portal do Azure'
description: Neste tutorial, você aprenderá a proteger sua rede de nuvem com o Gerenciador de firewall do Azure usando o portal do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501912"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>Tutorial: proteger sua rede de nuvem com a versão prévia do Gerenciador de firewall do Azure usando o portal do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Usando a visualização do Gerenciador de firewall do Azure, você pode criar hubs protegidos para proteger o tráfego de rede de nuvem destinado a endereços IP privados, PaaS do Azure e a Internet. O roteamento de tráfego para o firewall é automatizado, portanto, não é necessário criar rotas definidas pelo usuário (UDRs).

![proteger a rede na nuvem](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A versão prévia do Gerenciador de firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` PowerShell.

Em um prompt de comando do PowerShell, execute os seguintes comandos:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Demora até 30 minutos para que o registro do recurso seja concluído. Execute o seguinte comando para verificar o status do registro:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>Criar uma arquitetura de Hub e spoke

Primeiro, crie uma VNet spoke na qual você pode posicionar seus servidores.

### <a name="create-a-spoke-vnet-and-subnets"></a>Criar uma VNet e sub-redes spoke

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **rede**, selecione **rede virtual**.
4. Para **nome**, digite **spoke-01**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **criar novo**e digite **FW-Manager** para o nome e selecione **OK**.
8. Para **local**, selecione **(EUA) leste dos EUA**.
9. Em **sub-rede**, para o tipo de **nome** **carga de trabalho-SN**.
10. Em **Intervalo de endereços**, escreva **10.0.1.0/24**.
11. Aceite as outras configurações padrão e, em seguida, selecione **criar**.

Em seguida, crie uma sub-rede para um servidor de salto.

1. Na home page portal do Azure, selecione **grupos de recursos** > **FW-Manager**.
2. Selecione a rede virtual do **spoke-01** .
3. Selecione sub- **redes** >  **+ sub-rede**.
4. Para **nome**, digite **Jump-SN**.
5. Em **Intervalo de endereços**, escreva **10.0.2.0/24**.
6. Selecione **OK**.

### <a name="create-the-secured-virtual-hub"></a>Criar o Hub virtual protegido

Crie seu hub virtual protegido usando o Gerenciador de firewall.

1. No home page de portal do Azure, selecione **todos os serviços**.
2. Na caixa de pesquisa, digite **Gerenciador de firewall** e selecione **Gerenciador de firewall**.
3. Na página **Gerenciador de firewall** , selecione **criar um hub virtual protegido**.
4. Na página **criar novo hub virtual protegido** , selecione sua assinatura e o grupo de recursos do **Gerenciador de FW** .
5. Para o **nome do Hub virtual protegido**, digite **Hub-01**.
6. Para **local**, selecione **leste dos EUA**.
7. Para **espaço de endereço de Hub**, digite **10.1.0.0/16**.
8. Para o novo nome de vWAN, digite **vWAN-01**.
9. Deixe a caixa de seleção **incluir gateway de VPN para habilitar parceiros de segurança confiáveis** desmarcada.
10. Selecione **Avançar: Firewall do Azure**.
11. Aceite a configuração padrão do **Firewall do Azure** **habilitada** e, em seguida, selecione **Avançar: parceiro de segurança confiável**.
12. Aceite a configuração padrão de **parceiro de segurança confiável** **desabilitada** e selecione **Avançar: revisar + criar**.
13. Selecione **Criar**. Levará cerca de 30 minutos para ser implantado.

### <a name="connect-the-hub-and-spoke-vnets"></a>Conectar o Hub e o VNets do spoke

Agora você pode emparelhar o Hub e o spoke VNets.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione a WAN virtual **vwan-01** .
2. Em **conectividade**, selecione **conexões de rede virtual**.
3. Selecione **Adicionar conexão**.
4. Para **nome da conexão**, digite **hub-spoke**.
5. Para **hubs**, selecione **Hub-01**.
6. Para **rede virtual**, selecione **spoke-01**.
7. Selecione **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Criar uma política de firewall e proteger seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais hubs virtuais protegidos. Você criará sua política de firewall e protegerá seu hub.

1. No Gerenciador de firewall, selecione **criar uma política de firewall do Azure**.
2. Selecione sua assinatura e, em seguida, selecione o grupo de recursos **FW-Manager** .
3. Em **detalhes da política**, para o tipo de **nome** **política-01** e para **região** , selecione **leste dos EUA**.
4. Selecione **Avançar: regras**.
5. Na guia **regras** , selecione **Adicionar uma coleção de regras**.
6. Na página **Adicionar uma coleção de regras** , digite **RC-01** para o **nome**.
7. Para **tipo de coleção de regras**, selecione **aplicativo**.
8. Para **prioridade**, digite **100**.
9. Verifique se a **ação da coleção de regras** é **permitir**.
10. Para o **nome** da regra **, digite Allow-MSFT**.
11. Para **endereço de origem**, digite **\*** .
12. Para **protocolo**, digite **http, https**.
13. Verifique se * * tipo de destino é **FQDN**.
14. Para **destino**, digite **\*. Microsoft.com**.
15. Selecione **Adicionar**.
16. Selecione **Avançar: hubs virtuais protegidos**.
17. Na guia **hubs virtuais protegidos** , selecione **Hub-01**.
19. Selecione **Rever + criar**.
20. Selecione **Criar**.

Isso pode levar cerca de cinco minutos ou mais para ser concluído.

## <a name="route-traffic-to-your-hub"></a>Rotear o tráfego para o Hub

Agora, você deve garantir que o tráfego de rede seja roteado por meio do firewall.

1. No Gerenciador de firewall, selecione **hubs virtuais protegidos**.
2. Selecione **Hub-01**.
3. Em **configurações**, selecione **configurações de rota**.
4. Em **tráfego de Internet**, **tráfego de redes virtuais**, selecione **enviar por meio do firewall do Azure**.
5. Em **tráfego privado do Azure**, **tráfego para redes virtuais**, selecione **enviar por meio do firewall do Azure**.
6. Selecione **Editar prefixo (s) de endereço IP**.
7. Selecione **Adicionar um prefixo de endereço IP**.
8. Digite **10.0.1.0/24** como o endereço da sub-rede de carga de trabalho e selecione **salvar**.
9. Em **configurações**, selecione **conexões**.
10. Selecione a conexão **hub-spoke** e, em seguida, selecione **proteger o tráfego da Internet** e, em seguida, selecione **OK**.


## <a name="test-your-firewall"></a>Testar o firewall

Para testar suas regras de firewall, você precisará implantar alguns servidores. Você implantará a carga de trabalho-SRV na sub-rede da carga de trabalho-SN para testar as regras de firewall e ir para que você possa usar Área de Trabalho Remota para se conectar da Internet e, em seguida, conectar-se à carga de trabalho-SRV.

### <a name="deploy-the-servers"></a>Implantar os servidores

1. Na portal do Azure, selecione **criar um recurso**.
2. Selecione **Windows Server 2016 datacenter** na lista **popular** .
3. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**FW-Manager**|
   |Nome da máquina virtual     |**Ir para o servidor**|
   |Região     |**Digamos Leste dos EUA)**|
   |Nome de usuário do administrador     |**azureuser**|
   |Palavra-passe     |**Azure123456!**|

4. Em **regras de porta de entrada**, para **portas de entrada públicas**, selecione **permitir portas selecionadas**.
5. Para **selecionar portas de entrada**, selecione **RDP (3389)** .

6. Aceite os outros padrões e selecione **Avançar: discos**.
7. Aceite os padrões de disco e selecione **Avançar: rede**.
8. Verifique se o **spoke-01** está selecionado para a rede virtual e se a sub-rede é **Jump-SN**.
9. Para **IP público**, aceite o novo nome de endereço IP público padrão (Jump-SRV-IP).
11. Aceite os outros padrões e selecione **Avançar: gerenciamento**.
12. Selecione **desativado** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione **revisar + criar**.
13. Examine as configurações na página Resumo e, em seguida, selecione **criar**.

Use as informações na tabela a seguir para configurar outra máquina virtual chamada **Workload-SRV**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Valor  |
|---------|---------|
|Subrede|**Carga de trabalho-SN**|
|IP público|**None**|
|Portas de entrada públicas|**None**|

### <a name="add-a-route-table-and-default-route"></a>Adicionar uma tabela de rotas e uma rota padrão

Para permitir uma conexão com a Internet para o Jump-SRV, você deve criar uma tabela de rotas e uma rota de gateway padrão para a Internet a partir da sub-rede **Jump-SN** .

1. Na portal do Azure, selecione **criar um recurso**.
2. Digite **tabela de rotas** na caixa de pesquisa e selecione **tabela de rotas**.
3. Selecione **Criar**.
4. Digite **RT-01** para **nome**.
5. Selecione sua assinatura, o **Gerenciador de FW** para o grupo de recursos e **(EUA) leste dos EUA** para a região.
6. Selecione **Criar**.
7. Quando a implantação for concluída, selecione a tabela de rotas **RT-01** .
8. Selecione **rotas** e, em seguida, selecione **Adicionar**.
9. Digite **salto para a inet** para o **nome da rota**.
10. Digite **0.0.0.0/0** para o **prefixo de endereço**.
11. Selecione **Internet** para o **tipo do próximo salto**.
12. Selecione **OK**.
13. Quando a implantação for concluída, selecione **sub-redes**e, em seguida, selecione **associar**.
14. Selecione **spoke-01** para **rede virtual**.
15. Selecione **Jump-SN** para **sub-rede**.
16. Selecione **OK**.

### <a name="test-the-rules"></a>Testar as regras

Agora, teste as regras de firewall para confirmar se ele funciona conforme o esperado.

1. Na portal do Azure, examine as configurações de rede da máquina virtual de **carga de trabalho-SRV** e anote o endereço IP privado.
2. Conecte uma área de trabalho remota à máquina virtual de **Jump-SRV** e entre. A partir daí, abra uma conexão de área de trabalho remota para o endereço IP privado de **Workload-SRV** .

3. Abra o Internet Explorer e navegue até https://www.microsoft.com.
4. Selecione **OK** > **fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver o Microsoft home page.

5. Navegue para https://www.google.com.

   Deve estar bloqueado pela firewall.

Agora você verificou que as regras de firewall estão funcionando:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre parceiros de segurança confiáveis](trusted-security-partners.md)
