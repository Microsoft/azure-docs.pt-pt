---
title: Criar um gateway de aplicação com HTTP para redirecionamento a HTTPS com o portal do Azure
description: Saiba como criar um gateway de aplicação com o tráfego redirecionado de HTTP para HTTPS com o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 7aac6cfca3b5b6f36810d1fd88939929c14ac63b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308742"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Criar um gateway de aplicação com HTTP para redirecionamento a HTTPS com o portal do Azure

Pode utilizar o portal do Azure para criar uma [gateway de aplicação](overview.md) com um certificado para a terminação de SSL. Uma regra de roteamento é utilizada para redirecionar o tráfego HTTP para a porta HTTPS no seu gateway de aplicação. Neste exemplo, também de criar uma [conjunto de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para o conjunto de back-end do gateway de aplicação que contém duas instâncias de máquina virtual.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Configurar uma rede
> * Criar um gateway de aplicação com o certificado
> * Adicionar uma regra de serviço de escuta e o redirecionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais com o conjunto de back-end predefinido

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial requer o Azure PowerShell versão 1.0.0 do módulo ou posterior para criar um certificado e instalar o IIS. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Para executar os comandos neste tutorial, terá também de executar `Login-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para utilização em produção, deve importar um certificado válido assinado por um fornecedor fidedigno. Neste tutorial, crie um certificado autoassinado com [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Pode utilizar [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) com o Thumbprint que foi devolvido para exportar um ficheiro pfx do certificado.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Deverá ver algo semelhante a este resultado:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Utilize o thumbprint para criar o ficheiro pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Uma rede virtual é necessária para a comunicação entre os recursos que criar. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
3. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
4. Introduza estes valores para o gateway de aplicação:

    - *myAppGateway* - para o nome do gateway de aplicação.
    - *myResourceGroupAG* - para o novo grupo de recursos.

    ![Criar um novo gateway de aplicação](./media/create-url-route-portal/application-gateway-create.png)

5. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
6. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:

    - *myVNet* - para o nome da rede virtual.
    - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
    - *myAGSubnet* - para o nome da sub-rede.
    - *10.0.1.0/24* – para o espaço de endereços da sub-rede.

    ![Criar a rede virtual](./media/create-url-route-portal/application-gateway-vnet.png)

7. Clique em **OK** para criar a rede virtual e a sub-rede.
8. Sob **a configuração de IP de front-end**, certifique-se **tipo de endereço IP** é **pública**, e **criar novo** está selecionada. Introduza *myAGPublicIPAddress* para o nome. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
9. Sob **configuração do serviço de escuta**, selecione **HTTPS**, em seguida, selecione **selecionar um ficheiro** e navegue para o *c:\appgwcert.pfx* ficheiro e Selecione **aberto**.
10. Tipo *appgwcert* para o nome do certificado e *Azure123456!* para a palavra-passe.
11. Deixe o firewall de aplicações Web desativada e, em seguida, selecione **OK**.
12. Reveja as definições na página de resumo e, em seguida, selecione **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **todos os recursos** no menu do lado esquerdo e, em seguida, selecione **myVNet** na lista de recursos.
2. Selecione **sub-redes**e, em seguida, clique em **sub-rede**.

    ![Criar sub-rede](./media/create-url-route-portal/application-gateway-subnet.png)

3. Tipo *myBackendSubnet* para o nome da sub-rede.
4. Tipo *10.0.2.0/24* para o intervalo de endereços e, em seguida, selecione **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Adicionar uma regra de serviço de escuta e o redirecionamento

### <a name="add-the-listener"></a>Adicionar o serviço de escuta

Primeiro, adicione o serviço de escuta com o nome *myListener* para a porta 80.

1. Abra o **myResourceGroupAG** recursos de grupo e selecione **myAppGateway**.
2. Selecione **serviços de escuta** e, em seguida, selecione **+ básicas**.
3. Tipo *MyListener* para o nome.
4. Tipo *httpPort* para o novo nome de porta de front-end e *80* para a porta.
5. Certifique-se de que o protocolo está definido como **HTTP**e, em seguida, selecione **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Adicionar uma regra de roteamento com uma configuração de redirecionamento

1. No **myAppGateway**, selecione **regras** e, em seguida, selecione **+ básicas**.
2. Para o **Name**, tipo *Rule2*.
3. Certifique-se **MyListener** está selecionada para o serviço de escuta.
4. Selecione o **configurar o redirecionamento** caixa de verificação.
5. Para **tipo de redirecionamento**, selecione **permanente**.
6. Para **destino de redirecionamento**, selecione **serviço de escuta**.
7. Certifique-se a **serviço de escuta de destino** está definida como **appGatewayHttpListener**.
8. Selecione o **incluem a cadeia de consulta** e **caminho Include** caixas de verificação.
9. Selecione **OK**.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, vai criar um conjunto de dimensionamento de máquinas virtuais para fornecer servidores para o conjunto de back-end no gateway de aplicação.

1. No portal canto superior esquerdo, selecione **+ criar um recurso**.
2. Selecione **computação**.
3. Na caixa de pesquisa, escreva *conjunto de dimensionamento* e prima Enter.
4. Selecione **conjunto de dimensionamento de Máquina Virtual**e, em seguida, selecione **criar**.
5. Para **nome do conjunto de dimensionamento de máquinas virtuais**, tipo *myvmss*.
6. Para a imagem de disco do sistema operativo, * * garantir **Windows Server 2016 Datacenter** está selecionada.
7. Para **grupo de recursos**, selecione **myResourceGroupAG**.
8. Para **nome de utilizador**, tipo *azureuser*.
9. Para **palavra-passe**, tipo *Azure123456!* e confirme a palavra-passe.
10. Para **contagem de instâncias**, certifique-se de que o valor é **2**.
11. Para **tamanho da instância**, selecione **D2s_v3**.
12. Sob **redes**, certifique-se **opções de balanceamento de carga escolha** está definida como **Gateway de aplicação**.
13. Certifique-se **gateway de aplicação** está definida como **myAppGateway**.
14. Certifique-se **sub-rede** está definida como **myBackendSubnet**.
15. Selecione **Criar**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associar o conjunto de dimensionamento com o conjunto de back-end adequado

O portal de conjunto de dimensionamento de máquina virtual da interface do Usuário cria um novo conjunto de back-end para o conjunto de dimensionamento, mas pretende associá-lo a seu appGatewayBackendPool existente.

1. Abra o **myResourceGroupAg** grupo de recursos.
2. Selecione **myAppGateway**.
3. Selecione **conjuntos de back-end**.
4. Selecione **myAppGatewaymyvmss**.
5. Selecione **remover todos os destinos do conjunto de back-end**.
6. Selecione **Guardar**.
7. Após a conclusão deste processo, selecione o **myAppGatewaymyvmss** conjunto de back-end, selecione **eliminar** e, em seguida **OK** para confirmar.
8. Selecione **appGatewayBackendPool**.
9. Sob **destinos**, selecione **VMSS**.
10. Sob **VMSS**, selecione **myvmss**.
11. Sob **configurações de Interface de rede**, selecione **myvmssNic**.
12. Selecione **Guardar**.

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de dimensionamento

Por fim, tem de atualizar o conjunto de dimensionamento com estas alterações.

1. Selecione o **myvmss** conjunto de dimensionamento.
2. Sob **configurações**, selecione **instâncias**.
3. Selecione as duas instâncias e, em seguida, selecione **atualizar**.
4. Selecione **Sim** para confirmar.
5. Quando terminar, voltar para o **myAppGateway** e selecione **conjuntos back-end**. Agora, deverá ver que o **appGatewayBackendPool** tem dois destinos, e **myAppGatewaymyvmss** tem zero destinos.
6. Selecione **myAppGatewaymyvmss**e, em seguida, selecione **eliminar**.
7. Selecione **OK** para confirmar.

### <a name="install-iis"></a>Instalar o IIS

Uma forma fácil de instalar o IIS no conjunto de dimensionamento é utilizar o PowerShell. No portal, clique no ícone do Cloud Shell e certifique-se de que **PowerShell** está selecionada.

Cole o seguinte código na janela do PowerShell e prima Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de dimensionamento

Depois de alterar as instâncias com o IIS, mais uma vez tem de atualizar o conjunto de dimensionamento com esta alteração.

1. Selecione o **myvmss** conjunto de dimensionamento.
2. Sob **configurações**, selecione **instâncias**.
3. Selecione as duas instâncias e, em seguida, selecione **atualizar**.
4. Selecione **Sim** para confirmar.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Pode obter o endereço IP público do aplicativo de página de descrição geral do gateway de aplicação.

1. Selecione **myAppGateway**.
2. Sobre o **descrição geral** página, tome nota do endereço IP em **endereço IP público de front-end**.

3. Copie o endereço IP público e cole-o na barra de endereço do browser. Por exemplo, http://52.170.203.149

   ![Aviso de segurança](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Para aceitar o aviso de segurança se tiver utilizado um certificado autoassinado, selecione **Detalhes** e, em seguida **Aceda à página Web**. O site IIS protegido é apresentado como no exemplo seguinte:

   ![Testar o URL base no gateway de aplicação](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [criar um gateway de aplicação com o redirecionamento interno](redirect-internal-site-powershell.md).
