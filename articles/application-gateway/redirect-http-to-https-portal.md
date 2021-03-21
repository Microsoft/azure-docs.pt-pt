---
title: HTTP para https redirection no portal - Azure Application Gateway
description: Saiba como criar um gateway de aplicações com tráfego redirecionado de HTTP para HTTPS utilizando o portal Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397285"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Criar um gateway de aplicações com HTTP para https redirecionamento usando o portal Azure

Pode utilizar o portal Azure para criar um [gateway de aplicação](overview.md) com um certificado para a rescisão de TLS. Uma regra de encaminhamento é utilizada para redirecionar o tráfego HTTPS para a porta HTTPS no seu gateway de aplicações. Neste exemplo, também cria uma [balança de máquina virtual definida](../virtual-machine-scale-sets/overview.md) para o pool backend do gateway de aplicações que contém duas instâncias de máquina virtual.

Neste artigo, vai aprender a:

* Criar um certificado autoassinado
* Configurar uma rede
* Criar um gateway de aplicação com o certificado
* Adicione uma regra de ouvinte e redirecionamento
* Criar um conjunto de dimensionamento de máquinas virtuais com o conjunto de back-end predefinido

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial requer a versão 1.0.0 ou posterior do módulo Azure PowerShell para criar um certificado e instalar o IIS. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Para executar os comandos neste tutorial, também precisa correr `Login-AzAccount` para criar uma ligação com Azure.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Para uso de produção, deve importar um certificado válido assinado por um fornecedor de confiança. Neste tutorial, crie um certificado autoassinado com [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Pode utilizar [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) com o Thumbprint que foi devolvido para exportar um ficheiro pfx do certificado.

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

É necessária uma rede virtual para a comunicação entre os recursos que cria. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
3. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
4. Introduza estes valores para o gateway de aplicação:

   - *myAppGateway* - para o nome do gateway de aplicação.
   - *myResourceGroupAG* - para o novo grupo de recursos.

     ![Criar um novo gateway de aplicação](./media/create-url-route-portal/application-gateway-create.png)

5. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
6. Clique **em Escolher uma rede virtual,** clique em Criar **novo** e, em seguida, insira estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar a rede virtual](./media/create-url-route-portal/application-gateway-vnet.png)

7. Clique em **OK** para criar a rede virtual e a sub-rede.
8. Na **configuração IP frontend,** certifique-se de que o **tipo de endereço IP** é **público,** e **crie novos.** Insira *myAGPublicIPAddress* para o nome. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
9. Na **configuração do Ouvinte**, selecione **HTTPS**, em seguida, **selecione Selecione um ficheiro** e navegue para o ficheiro *c:\appgwcert.pfx* e selecione **Open**.
10. Escreva *appgwcert* para o nome cert e *Azure123456!* para a palavra-passe.
11. Deixe a firewall da aplicação Web desativada e, em seguida, selecione **OK**.
12. Reveja as definições na página do resumo e, em seguida, selecione **OK** para criar os recursos de rede e o gateway de aplicação. Pode levar vários minutos para a porta de entrada de aplicação ser criada, aguardar até que a colocação termine com sucesso antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **myVNet** da lista de recursos.
2. Selecione **sub-redes** e, em seguida, clique na **Sub-rede**.

    ![Criar sub-rede](./media/create-url-route-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede.
4. Tipo *10.0.2.0/24* para a gama de endereços e, em seguida, selecione **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Adicione uma regra de ouvinte e redirecionamento

### <a name="add-the-listener"></a>Adicione o ouvinte

Primeiro, adicione o ouvinte chamado *myListener* para o porto 80.

1. Abra o grupo de recursos **myResourceGroupAG** e selecione **myAppGateway**.
2. Selecione **Os Ouvintes** e, em seguida, selecione **+ Básico**.
3. Digite *MyListener* para o nome.
4. Digite *httpPort* para o novo nome do porta frontal e *80* para o porto.
5. Certifique-se de que o protocolo está definido para **HTTP**, e, em seguida, selecione **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Adicione uma regra de encaminhamento com uma configuração de redirecionamento

1. No **myAppGateway,** selecione **Regras** e, em seguida, selecione **+Request encaminhamento regra**.
2. Para o **nome da Regra**, escreva a *Regra2*.
3. Certifique-se de que **o MyListener** está selecionado para o ouvinte.
4. Clique no **separador alvos de Backend** e selecione **o tipo de alvo** como *Redirection*.
5. Para **o tipo de redirecionamento,** selecione **Permanente**.
6. Para **o alvo de reorientação**, selecione **Listener**.
7. Certifique-se de que o **ouvinte target** está definido para **appGatewayHttpListener**.
8. Para incluir a **cadeia de consulta** e incluir o **caminho** selecione *Sim*.
9. Selecione **Adicionar**.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, vai criar um conjunto de dimensionamento de máquinas virtuais para fornecer servidores para o conjunto de back-end no gateway de aplicação.

1. No canto superior esquerdo do portal, selecione **+Criar um recurso**.
2. Selecione **Computação**.
3. Na caixa de pesquisa, escreva *o conjunto* de escala e prima Enter.
4. Selecione **o conjunto de escala de máquina virtual** e, em seguida, selecione **Criar**.
5. Para **o nome de conjunto de escala de máquina virtual,** digite *myvmss*.
6. Para a imagem do disco do sistema operativo,** certifique-se de que o **Centro de Dados do Windows Server 2016** está selecionado.
7. Para **o grupo de recursos**, selecione **myResourceGroupAG**.
8. Para **o nome do utilizador,** *escreva azureuser*.
9. Para **senha**, *escreva Azure123456!* e confirmar a senha.
10. Por **exemplo, certifique-se** de que o valor é **2**.
11. Por **exemplo, selecione** **D2s_v3**.
12. Em **Rede**, certifique-se **de que as opções de equilíbrio de carga** escolhidas estão definidas para o Gateway de **Aplicações**.
13. Certifique-se de que **o gateway de aplicações** está definido para **o meuAppGateway**.
14. Certifique-se de que a **Subnet** está definida para **o myBackendSubnet**.
15. Selecione **Criar**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associe o conjunto de escala com a piscina de backend adequada

O portal de conjunto de escala de máquina virtual UI cria um novo pool de backend para o conjunto de escala, mas você deseja associá-lo com a sua app existenteGatewayBackendPool.

1. Abra o grupo de recursos **myResourceGroupAg.**
2. Selecione **myAppGateway**.
3. Selecione **piscinas backend**.
4. Selecione **myAppGatewaymyvmss**.
5. **Selecione Remova todos os alvos da piscina de backend**.
6. Selecione **Guardar**.
7. Depois de concluído este processo, selecione o pool de backend **myAppGatewaymyvmss,** selecione **Delete** e, em seguida, **OK** para confirmar.
8. Selecione **appGatewayBackendPool**.
9. Em **Alvos**, selecione **VMSS**.
10. Em **VMSS,** selecione **myvmss**.
11. Em **Configurações de Interface de Rede**, selecione **myvmssNic**.
12. Selecione **Guardar**.

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de escala

Finalmente, tem de atualizar a escala definida com estas alterações.

1. Selecione o conjunto de escala **de myvmss.**
2. Em **Definições**, selecione **Instâncias**.
3. Selecione ambas as instâncias e, em seguida, **selecione Upgrade**.
4. Selecione **Sim** para confirmar.
5. Depois de isto concluído, volte ao **myAppGateway** e selecione **backend pools**. Deve agora ver que a **appGatewayBackendPool** tem dois alvos, e  **o meuAppGatewaymyvmss** não tem alvos.
6. Selecione **myAppGatewaymyvmss** e, em seguida, **selecione Delete**.
7. Selecione **OK** para confirmar.

### <a name="install-iis"></a>Instalar o IIS

Uma maneira fácil de instalar o IIS no conjunto de escala é utilizar o PowerShell. A partir do portal, clique no ícone Cloud Shell e certifique-se de que **o PowerShell** está selecionado.

Cole o seguinte código na janela PowerShell e prima Enter.

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

### <a name="upgrade-the-scale-set"></a>Atualizar o conjunto de escala

Depois de alterar as instâncias com o IIS, deve atualizar novamente a escala definida com esta alteração.

1. Selecione o conjunto de escala **de myvmss.**
2. Em **Definições**, selecione **Instâncias**.
3. Selecione ambas as instâncias e, em seguida, **selecione Upgrade**.
4. Selecione **Sim** para confirmar.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Pode obter o endereço IP público da aplicação na página 'Vista Geral' do gateway da aplicação.

1. Selecione **myAppGateway**.
2. Na página **'Visão Geral',** note o endereço IP no **endereço IP público Frontend**.

3. Copie o endereço IP público e cole-o na barra de endereço do browser. Por exemplo, http://52.170.203.149

   ![Aviso de segurança](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Para aceitar o aviso de segurança se usou um certificado auto-assinado, selecione **Detalhes** e, em seguida, **vá para a página web**. O site IIS protegido é apresentado como no exemplo seguinte:

   ![Testar o URL base no gateway de aplicação](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar uma porta de aplicação com reorientação interna.](redirect-internal-site-powershell.md)