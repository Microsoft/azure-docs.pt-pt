---
title: Guia de implantação fortiGate / Microsoft Docs
description: Instale-se e trabalhe com o produto de firewall fortinet FortiGate de próxima geração.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273335"
---
# <a name="fortigate-deployment-guide"></a>Guia de implantação fortiGate

Utilizando este guia de implementação, aprende-se a configurar e a trabalhar com o produto de firewall fortinet FortiGate de próxima geração.

## <a name="redeem-the-fortigate-license"></a>Resgatar a licença FortiGate

O produto fortinet FortiGate de próxima geração está disponível como uma máquina virtual na infraestrutura Azure como um serviço (IaaS). Existem dois modos de licenciamento para esta máquina virtual: pay-as-you-go e trazer a sua própria licença.

A Fortinet poderá fornecer licenças aos membros da equipa Azure Ative Directory (Azure AD) "Get to Production Secure Hybrid Access (SHA)". Nos casos em que não tenha sido fornecida qualquer licença, a implantação pay-as-you-go também funcionará.

Se uma licença tiver sido emitida, a Fortinet fornece um código de registo que deve ser resgatado online.

![Screenshot do código de registo VPN FortiGate SSL.](registration-code.png)

1. Registe-se em https://support.fortinet.com/ .
2. Após a inscrição, inscreva-se em https://support.fortinet.com/ .
3. Vá **Asset**ao  >  **Registo de Ativos/Ativar**.
4. Introduza o código de registo fornecido por Fortinet.
5. Especificar o código de registo, selecionar **O produto será utilizado por um utilizador não governamental**e selecione **Next**.
6. Introduza uma descrição do produto (por exemplo, FortiGate), destine o parceiro Fortinet como **Other**  >  **Microsoft**, e selecione **Next**.
7. Aceite o **Acordo de Registo do Produto Fortinet**e selecione **Seguinte**.
8. Aceite os **Termos** e selecione **Confirmar.**
9. Selecione o **Download de Ficheiros de Licença**e guarde a licença para mais tarde.


## <a name="download-firmware"></a>Baixar firmware

O Fortinet FortiGate Azure VM não envia atualmente a versão de firmware necessária para a autenticação SAML. A versão mais recente deve ser obtida a partir de Fortinet.

1. Inscreva-se em https://support.fortinet.com/ .
2. Vá para **baixar**  >  **imagens de firmware**.
3. À direita das Notas de **Lançamento**, selecione **Download**.
4. Selecione **v6.** > **6.** > **6.4.**. .
5. Descarregue **FGT_VM64_AZURE-v6-build1637-FORTINET.out** selecionando o link **HTTPS** na mesma linha.
6. Guarde o ficheiro para mais tarde.


## <a name="deploy-the-fortigate-vm"></a>Implementar o VM FortiGate

1. Vá a https://portal.azure.com , e inscreva-se na subscrição na qual pretende implantar a máquina virtual FortiGate.
2. Crie um novo grupo de recursos ou abra o grupo de recursos no qual pretende implantar a máquina virtual FortiGate.
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

1. Vá a https://portal.azure.com , e abra as definições para o VM FortiGate.
2. No ecrã **geral,** selecione o endereço IP público.

    ![Screenshot de Fortigate SSL VPN.](public-ip-address.png)

3. Selecione **Static**  >  **Static Save**.

Se possuir um nome de domínio publicamente rotaível para o ambiente em que o VM FortiGate está a ser implantado, crie um registo de Hospedeiro (A) para o VM. Este registo mapeia para o endereço IP público anterior que é atribuído estáticamente.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Criar uma nova regra do grupo de segurança de rede de entrada para a porta TCP

1. Vá a https://portal.azure.com , e abra as definições para o VM FortiGate.
2. No menu à esquerda, selecione **Networking**. A interface de rede está listada e as regras da porta de entrada são mostradas.
3. **Selecione Adicionar a regra da porta de entrada**.
4. Criar uma nova regra de entrada para TCP 8443.

    ![Screenshot da regra de segurança de entrada de entrada.](port-rule.png)

5. Selecione **Adicionar**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Crie uma app Azure personalizada para FortiGate

1. Vá até https://portal.azure.com , e abra o painel AD AZure para o inquilino que fornecerá a identidade para os sign-ins fortiGate.
2. No menu esquerdo, selecione **Aplicações empresariais.**
3. Selecione **nova**  >  **aplicação não-galeria**.
4. Introduza um nome (por exemplo, FortiGate) e **selecione Adicionar**.
5. No menu esquerdo, selecione **Utilizadores e grupos**.
6. Adicione utilizadores que poderão iniciar sessão e selecione **Atribua.**
7. No menu esquerdo, selecione **Single sign-on**.
8. Selecione **SAML**.
9. Na **Configuração Básica SAML,** selecione o ícone do lápis para editar a configuração.
10. Configure o seguinte:
    - **Identificador (ID de entidade)** a ser `https://<address>/remote/saml/metadata` .
    - **URL de resposta (URL de serviço ao consumidor de afirmação)** a ser  `https://<address>/remote/saml/login` .
    - **URL logout** para ser `https://<address>/remote/saml/logout` .

    `<address>` é o FQDN ou o endereço IP público atribuído ao LM FortiGate.

11. Grave cada um destes URLs para utilização posterior: ID de entidade, URL de resposta e URL de logo.
12. **Selecione Guardar**e fechar **a configuração básica do SAML**.
13. **Subse para menos de 3 – Certificado de Assinatura SAML,** **descarregue o Certificado (Base64)** e guarde-o para mais tarde.
14. Abaixo **de 4 – Configurar (Nome da App)**, copiar o URL de Login Azure, identificador AD Azure e URL Azure Logout, e guardá-los para mais tarde.
15. Abaixo **de 2 – Atributos e Reclamações do Utilizador**, selecione o ícone do lápis para editar a configuração.
16. **Selecione Adicione nova reclamação**e desconte o nome **de utilizador**.
17. Desa estama de origem ao **nome user.userprincipal .**
18. Selecione **Save**  >  **Add uma reivindicação de grupo**Todos os  >  **grupos**.
19. **Selecione Personalizar o nome da reclamação do grupo**e definir o nome para **grupo**.
20. Selecione **Guardar**.


## <a name="prepare-for-group-matching"></a>Preparar para a correspondência de grupo

O FortiGate permite diferentes experiências no portal do utilizador após a sindução, com base na adesão ao grupo. Por exemplo, pode haver uma experiência para o grupo de marketing e outra para o grupo financeiro. É assim que se criam grupos para os utilizadores:

1. Vá até https://portal.azure.com , e abra o painel AD AZure para o inquilino que fornecerá a identidade para os sign-ins fortiGate.
2. Selecione **Grupos**  >  **Novos Grupos**.
3. Criar um grupo com os seguintes detalhes:
    - Tipo de grupo = Segurança
    - Nome do grupo = `a meaningful name`
    - Descrição do grupo = `a meaningful description for the group`
    - Tipo de adesão = Atribuído
    - Membros = `users for the user experience that will map to this group`
4. Repita os passos 3 e 4 para quaisquer experiências adicionais do utilizador.
5. Depois de criar os grupos, selecione cada grupo e grave o **ID** do objeto para cada um.
6. Guarde estes IDs de objeto e nomes de grupo para mais tarde.


## <a name="configure-the-fortigate-vm"></a>Configure o VM FortiGate

As seguintes secções acompanham-no como configurar o LM FortiGate.

### <a name="install-the-license"></a>Instalar a licença

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. Se a implementação usar o modelo de trazer a sua própria licença, verá um pedido para enviar uma licença. Selecione o ficheiro de licença criado anteriormente e carreve-o. Selecione **OK** e reinicie o FortiGate VM.

    ![Screenshot da Licença FortiGate VM.](license.png)

5. Após o reboot, inscreva-se novamente com as credenciais do administrador para validar a licença.

### <a name="update-firmware"></a>Atualizar firmware

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, selecione **System**  >  **Firmware**.
5. Na **Firmware Management**, selecione **Browse**e selecione o ficheiro firmware descarregado anteriormente.
6. Ignore o aviso e selecione **Backup config e upgrade**.

    ![Screenshot da Firmware Management.](backup-configure-upgrade.png)

7. Selecione **Continuar**.
8. Quando lhe for solicitado que guarde a configuração do FortiGate (como ficheiro .conf), **selecione Save**.
9. Aguarde que o firmware faça o upload e seja aplicado. Aguarde que o LM FortiGate reinicie.
10. Após o reinício do LM Do FortiGate, inscreva-se novamente com as credenciais do administrador.
11. Quando lhe for solicitado que instale o painel de instrumentos, selecione **Mais tarde**.
12. Quando o vídeo tutorial começar, selecione **OK**.

### <a name="change-the-management-port-to-tcp"></a>Mude o porto de gestão para TCP

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, selecione **Sistema**.
5. Em **Definições de Administração**, altere a porta HTTPS para **8443**, e selecione **Aplicar**.
6. Após a alteração, o navegador tenta recarregar a página de administração, mas falha. A partir de agora, o endereço da página da administração é `https://<address>` .

    ![Screenshot do Certificado Remoto de Upload.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Faça o upload do certificado de assinatura Azure AD SAML

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, **System**selecione  >  **Certificates**do Sistema.
5. Selecione Certificado Remoto **de Importação**  >  **Remote Certificate**.
6. Navegue pelo certificado descarregado a partir da implementação de aplicações personalizadas FortiGate no inquilino Azure. Selecione-o e selecione **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Faça upload e configurar um certificado SSL personalizado

É melhor configurar o FM VM fortiGate com o seu próprio certificado SSL que suporta o FQDN que está a usar. Se tiver acesso a um certificado SSL embalado com a chave privada em formato PFX, pode ser utilizado para o efeito.

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Continue além de quaisquer erros de certificado.
3. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
4. No menu esquerdo, **System**selecione  >  **Certificates**do Sistema.
5. **Selecione**  >  **Import Local Certificate**  >  **PKCS #12 Certificate**.
6. Navegue pelo . Ficheiro PFX que contém o certificado SSL e a chave privada.
7. Providenciar o . Senha PFX, e um nome significativo para o certificado. Em seguida, selecione **OK**.
8. No menu esquerdo, selecione **Definições do Sistema**  >  **Settings**.
9. Em **Definições de Administração,** expanda a lista ao lado do **certificado do servidor HTTPS**e selecione o certificado SSL importado anteriormente.
10. Selecione **Aplicar**.
11. Feche a janela do navegador e vá para `https://<address>` .
12. Inscreva-se com as credenciais de administrador do FortiGate. Deverá agora ver o certificado SSL correto em uso.


### <a name="perform-command-line-configuration"></a>Executar a configuração da linha de comando

As seguintes secções fornecem passos para várias configurações utilizando a linha de comando.

#### <a name="for-saml-authentication"></a>Para autenticação SAML

1. Vá a https://portal.azure.com , e abra as definições para o VM FortiGate.
2. No menu esquerdo, selecione **Serial Console**.
3. Inscreva-se na Consola em Série com as credenciais de administrador de VM FortiGate. Para o próximo passo, os URLs que gravou anteriormente são necessários:
    - ID de entidade
    - URL de Resposta
    - Logout URL
    - Azure Login URL
    - Identificador de Azure Ad
    - Azure Logout URL
4. Na Consola em Série, executar os seguintes comandos:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > O URL Azure Logout contém um `?` personagem. Isto requer uma sequência de chave especial para que seja fornecida corretamente à Consola em Série FortiGate. O URL é `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` tipicamente. Para fornecer isto na Consola em Série, escreva:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Em seguida, digite CTRL+V e cole o resto do URL para completar a linha: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Para confirmar a configuração, execute o seguinte:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Para combinar em grupo

1. Vá a https://portal.azure.com , e abra as definições para o VM FortiGate.
2. No menu esquerdo, selecione **Serial Console**.
3. Inscreva-se na Consola em Série com as credenciais de administrador de VM FortiGate.
4. Na Consola em Série, executar os seguintes comandos:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Para cada grupo adicional que terá uma experiência de portal diferente no FortiGate, repita estes comandos (a começar pela segunda linha do código).

#### <a name="for-authentication-timeout"></a>Para tempo de autenticação

1. Vá a https://portal.azure.com , e abra as definições para o VM FortiGate.
2. No menu esquerdo, selecione **Serial Console**.
3. Inscreva-se na Consola em Série com as credenciais de administrador de VM FortiGate.
4. Na Consola em Série, executar os seguintes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Criar portais VPN e política de firewall

1. Aceda a `https://<address>`. Aqui, `<address>` o FQDN ou o endereço IP público atribuído ao LM FortiGate.

2. Inscreva-se utilizando as credenciais de administrador fornecidas durante a implementação do FortiGate VM.
3. No menu esquerdo, selecione **VPN**  >  **SSL-VPN Portals**  >  **Create New**.
6. Forneça um nome (normalmente correspondendo-o ao grupo Azure usado para proporcionar a experiência personalizada do portal).
7. Selecione o sinal de mais ( **+** ) ao lado de **Pools IP Fonte,** selecione o pool predefinido e, em seguida, selecione **Fechar**.
8. Personalize a experiência para este grupo. Para testes, esta pode ser a personalização da mensagem do portal e do tema. É também aqui que pode criar marcadores personalizados que direcionam os utilizadores para recursos internos.
9. Selecione **OK**.
10. Repita os passos 5-9 para cada grupo Azure que terá uma experiência personalizada no portal.
11. Em VPN, selecione **SSL-VPN Definições**.
12. Selecione o sinal de mais ( **+** ) ao lado de Ouvir nas **Interfaces,** selecione **Port1**e, em seguida, selecione **Fechar**.
14. Se instalou previamente um certificado SSL personalizado, altere o **Certificado de Servidor** para utilizar o certificado SSL personalizado no menu suspenso.
15. Em **Autenticação/Mapeamento do Portal,** selecione **Criar Novo**. Escolha o primeiro grupo Azure e combine-o com o portal do mesmo nome. Em seguida, selecione **OK**.
18. Repita os passos 15-17 para cada emparelhamento de um grupo Azure e um portal.
19. Em **Autenticação/Mapeamento do Portal,** edite **todos os outros utilizadores/grupos**.
20. Desacione o portal para **o acesso completo**e selecione **OK**  >  **Apply**.
23. Percorra o topo da página **de Definição SSL-VPN** e selecione **não existem políticas SSL-VPN. Clique aqui para criar uma nova política SSL-VPN usando estas definições.**
24. Fornecer um nome, como **VPN Grp**. Em seguida, desacione **a Interface de Saída** para a **porta**, e selecione **Source**.
27. No **Endereço**, selecione **todos**.
28. Em **User**, selecione o primeiro grupo Azure.
29. Selecione **Close**  >  **Destino Próximo**. Em **'Endereço' ( 'Morada'**( isto é normalmente a rede interna). Selecione **login.microsoft.com** para testes.
32. Selecione **Serviço de Proximidade**  >  **Service**  >  **Tudo**. Em seguida, **selecione Close**  >  **OK**.
37. No menu à esquerda, selecione Política & Política de Firewall **de Objetos**  >  **Firewall Policy**.
39. Expandir **a interface do túnel SSL-VPN (ssl.root).**  >  **port**
40. Clique com o direito na política VPN criada anteriormente **(VPN Grp 1**), e selecione **Copy**.
41. Clique à direita sob a política VPN e **selecione Pasta**  >  **Abaixo**.
42. Editar a nova política, proporcionando-lhe um nome diferente (por exemplo, **VPN Grp2).** Altere também o grupo a que se aplica (para outro grupo Azure).
43. Clique com o direito na nova política e desa estalido o estado de **Ativação**.


## <a name="test-sign-in-by-using-azure"></a>Teste de s-in utilizando Azure

1. Usando uma sessão de navegador privada, vá a `https://<address>` .  
2. O sin-in deve ser redirecionado para Azure AD para iniciar sação.
3. Depois de fornecer credenciais para um utilizador que foi designado para a aplicação FortiGate no inquilino Azure, deve aparecer o portal de utilizador apropriado.

    ![Screenshot do FortiGate SSL VPN](test-sign-in.png)
