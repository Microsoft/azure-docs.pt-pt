---
title: Guia de implantação fortiGate / Microsoft Docs
description: Instale-se e trabalhe com o produto de firewall fortinet FortiGate de próxima geração.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025528"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Guia de implantação de máquinas virtuais FortiGate Azure

Utilizando este guia de implementação, aprenderá a configurar e trabalhar com o produto de firewall fortinet FortiGate de próxima geração implantado como uma Máquina Virtual Azure. Além disso, irá configurar a App fortiGate SSL VPN AD Gallery para fornecer autenticação VPN através do Azure Ative Directory.

## <a name="redeem-the-fortigate-license"></a>Resgatar a Licença FortiGate

O produto fortinet FortiGate de próxima geração está disponível como uma máquina virtual na infraestrutura Azure como um serviço (IaaS). Existem dois modos de licenciamento para esta máquina virtual: pay-as-you-go e bring-your-own-license (BYOL).

Se adquiriu uma licença FortiGate da Fortinet para utilizar com a opção de implementação da máquina virtual BYOL, resgatá-la na página de ativação do produto da Fortinet – https://support.fortinet.com . O ficheiro de licença resultante terá uma extensão de ficheiro .lic.

## <a name="download-firmware"></a>Baixar Firmware

No momento da escrita, o Fortinet FortiGate Azure VM não envia com a versão firmware necessária para a autenticação SAML. A versão mais recente deve ser obtida a partir de Fortinet.

1. Inscreva-se em https://support.fortinet.com/ .
2. Vá para **baixar**  >  **imagens de firmware**.
3. À direita das Notas de **Lançamento**, selecione **Download**.
4. Selecione **v6.00**  >  **6.4**  >  **6.4.2**.
5. Descarregue **FGT_VM64_AZURE-v6-build1723-FORTINET.out** selecionando o link **HTTPS** na mesma linha.
6. Guarde o ficheiro para mais tarde.

## <a name="deploy-the-fortigate-vm"></a>Implementar o VM FortiGate

1. Vá ao portal Azure e inscreva-se na subscrição na qual irá implantar a máquina virtual FortiGate.
2. Crie um novo grupo de recursos ou abra o grupo de recursos no qual irá implantar a máquina virtual FortiGate.
3. Selecione **Adicionar**.
4. Em **Search the Marketplace,** *insira Forti*. Selecione **Fortinet FortiGate Next Generation Firewall**.
5. Selecione o plano de software (traga a sua própria licença se tiver uma licença, ou pagar como você vai se não). Selecione **Criar**.
6. Povoar a configuração VM.

    ![Screenshot de Criar uma máquina virtual.](virtual-machine.png)

7. Desajuste **o tipo de autenticação** para **a Palavra-passe,** e forneça credenciais administrativas para o VM.
8. Selecione **Rever + Criar** > **Criar**.
9. Aguarde a conclusão da implantação de VM.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Desaponione um endereço IP público estático e atribua um nome de domínio totalmente qualificado

Para uma experiência de utilizador consistente, desajuste o endereço IP público atribuído ao VM FortiGate para ser atribuído estáticamente. Além disso, mapeie-o para um nome de domínio totalmente qualificado (FQDN).

1. Vá ao portal Azure e abra as definições para o LM FortiGate.
2. No ecrã **geral,** selecione o endereço IP público.

    ![Screenshot de Fortigate SSL VPN.](public-ip-address.png)

3. Selecione **Static**  >  **Static Save**.

Se possuir um nome de domínio publicamente rotaível para o ambiente em que o VM FortiGate está a ser implantado, crie um registo de Hospedeiro (A) para o VM. Este registo mapeia para o endereço IP público anterior que é atribuído estáticamente.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Criar uma nova regra do grupo de segurança da rede de entrada para a porta TCP 8443

1. Vá ao portal Azure e abra as definições para o LM FortiGate.
2. No menu à esquerda, selecione **Networking**. A interface de rede está listada e as regras da porta de entrada são mostradas.
3. **Selecione Adicionar a regra da porta de entrada**.
4. Criar uma nova regra de entrada para TCP 8443.

    ![Screenshot da regra de segurança de entrada de entrada.](port-rule.png)

5. Selecione **Adicionar**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Criar um Segundo NIC Virtual para o VM

Para que os recursos internos sejam disponibilizados aos utilizadores, um segundo NIC Virtual deve ser adicionado ao VM FortiGate. A Rede Virtual em Azure em que reside o NIC Virtual deve ter uma ligação encaminhável a esses recursos internos.

1. Vá ao portal Azure e abra as definições para o LM FortiGate.
2. Se o VM FortiGate ainda não estiver parado, **selecione Stop** e aguarde que o VM desligue.
3. No menu à esquerda, selecione **Networking**.
4. Selecione **Fixe a interface de rede**.
5. Selecione **Criar e anexar interface de rede.**
6. Configure as propriedades para a nova interface de rede e, em seguida, selecione **Criar**.

    ![Screenshot da interface de rede Create.](new-network-interface.png)

7. Inicie o FortiGate VM.


## <a name="configure-the-fortigate-vm"></a>Configure o VM FortiGate

As seguintes secções acompanham-no como configurar o LM FortiGate.

### <a name="install-the-license"></a>Instalar a Licença

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. Se a implementação usar o modelo de trazer a sua própria licença, verá um pedido para enviar uma licença. Selecione o ficheiro de licença criado anteriormente e carreve-o. Selecione **OK** e reinicie o FortiGate VM.

    ![Screenshot da Licença FortiGate VM.](license.png)

5. Após o reboot, inscreva-se novamente com as credenciais do administrador para validar a licença.

### <a name="update-firmware"></a>Atualizar Firmware

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, selecione **System**  >  **Firmware**.
5. Na **Firmware Management**, selecione **Browse** e selecione o ficheiro firmware descarregado anteriormente.
6. Ignore o aviso e selecione **Backup config e upgrade**.

    ![Screenshot da Firmware Management.](backup-configure-upgrade.png)

7. Selecione **Continuar**.
8. Quando lhe for solicitado que guarde a configuração do FortiGate (como ficheiro .conf), **selecione Save**.
9. Aguarde que o firmware faça o upload e seja aplicado. Aguarde que o LM FortiGate reinicie.
10. Após o reinício do LM Do FortiGate, inscreva-se novamente com as credenciais do administrador.
11. Quando lhe for solicitado que instale o painel de instrumentos, selecione **Mais tarde**.
12. Quando o vídeo tutorial começar, selecione **OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>Alterar o Porto de Gestão para TCP 8443

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, selecione **Sistema**.
5. Em **Definições de Administração**, altere a porta HTTPS para **8443**, e selecione **Aplicar**.
6. Após a alteração, o navegador tenta recarregar a página de administração, mas falha. A partir de agora, o endereço da página da administração é `https://<address>:8443` .

    ![Screenshot do Certificado Remoto de Upload.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Faça o upload do Certificado de Assinatura Azure AD SAML

1. Aceda a `https://<address>:8443`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, **System** selecione  >  **Certificates** do Sistema.
5. Selecione Certificado Remoto **de Importação**  >  **Remote Certificate**.
6. Navegue pelo certificado descarregado a partir da implementação de aplicações personalizadas FortiGate no inquilino Azure. Selecione-o e selecione **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Faça upload e configurar um certificado SSL personalizado

É melhor configurar o FM VM fortiGate com o seu próprio certificado SSL que suporta o FQDN que está a usar. Se tiver acesso a um certificado SSL embalado com a chave privada em formato PFX, pode ser utilizado para o efeito.

1. Aceda a `https://<address>:8443`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, **System** selecione  >  **Certificates** do Sistema.
5. **Selecione**  >  **Import Local Certificate**  >  **PKCS #12 Certificate**.
6. Navegue pelo . Ficheiro PFX que contém o certificado SSL e a chave privada.
7. Providenciar o . Senha PFX, e um nome significativo para o certificado. Em seguida, selecione **OK**.
8. No menu esquerdo, selecione **Definições do Sistema**  >  **Settings**.
9. Em **Definições de Administração,** expanda a lista ao lado do **certificado do servidor HTTPS** e selecione o certificado SSL importado anteriormente.
10. Selecione **Aplicar**.
11. Feche a janela do navegador e vá para `https://<address>:8443` .
12. Inscreva-se com as credenciais de administrador do FortiGate. Deverá agora ver o certificado SSL correto em uso.

### <a name="configure-authentication-timeout"></a>Configurar o tempo limite de autenticação

1. Vá ao portal Azure e abra as definições para o LM FortiGate.
2. No menu esquerdo, selecione **Serial Console**.
3. Inscreva-se na Consola em Série com as credenciais de administrador de VM FortiGate.
4. Na Consola em Série, executar os seguintes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Garantir que as interfaces de rede estão a obter endereços IP

1. Aceda a `https://<address>:8443`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
3. No menu à esquerda, selecione **Networking**.
4. Em Rede, selecione **Interfaces.**
5. Examine a porta1 (interface externa) e a porta2 (interface interna) para garantir que estão a obter um endereço IP a partir da sub-rede Azure correta.
    a. Se uma das portas não estiver a obter um endereço IP a partir da sub-rede (via DHCP), clique à direita na porta e **selecione Editar**.
    b. Ao lado do Modo de Endereçamento, certifique-se de que o **DHCP** está selecionado.
    c. Seelct **OK.**

    ![Screenshot do Endereço de Interface de Rede.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Garantir que a FortiGate VM tem a rota correta para recursos corporativos no local

Os VMs Azure multi-algarídos têm todas as interfaces de rede na mesma rede virtual (mas talvez sub-redes separadas). Isto significa frequentemente que ambas as interfaces de rede têm uma ligação com os recursos corporativos no local que estão a ser publicados via FortiGate. Por esta razão, é necessário criar entradas de rota personalizadas que garantam saídas de tráfego da interface correta quando são feitos pedidos de recursos corporativos no local.

1. Aceda a `https://<address>:8443`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM.
3. No menu à esquerda, selecione **Networking**.
4. Em Rede, selecione **Rotas Estáticas.**
5. Selecione **Criar Nova**.
6. Ao lado do Destino selecione **Sub-rede**.
7. No âmbito da Sub-rede, especificar as informações da sub-rede onde residem os recursos corporativos no local (por exemplo, 10.1.0.0/255.255.255.0)
8. Ao lado do Gateway Address especificar o portal na sub-rede Azure onde a porta2 está ligada (por exemplo, isto geralmente termina em 1 como 10.6.1.1)
9. Ao lado da Interface selecione a interface interna de rede, porta2
10. Selecione **OK**.

    ![Screenshot de Configurar uma Rota.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Configure FortiGate SSL VPN

Siga os passos delineados em https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial
