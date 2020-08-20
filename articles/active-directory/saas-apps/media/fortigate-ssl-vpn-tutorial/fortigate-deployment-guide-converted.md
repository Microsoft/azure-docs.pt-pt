---
title: Guia de implantação do FortiGate ! Microsoft Docs
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FortiGate SSL VPN.
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658451"
---
# <a name="fortigate-deployment-guide"></a>Guia de implementação do FortiGate

## <a name="contents"></a>Conteúdos

- Redentora da Licença FortiGate
- Baixar Firmware
- Implementar o VM FortiGate
   - Desconfie de um endereço IP público Statuc e atribua um nome de domínio totalmente qualificado
   - Criar uma nova regra do grupo de segurança da rede de entrada para a porta TCP
- Crie uma app Azure personalizada para FortiGate
- Preparar para a correspondência do grupo
   - Criar Grupos para Utilizadores
- Configure o VM FortiGate
   - Instalar a Licença
   - Atualizar Firmware
   - Alterar o Porto de Gestão para TCP
   - Faça o upload do Certificado de Assinatura SAML do Diretório Ativo Azure
   - Upload e Configurar um Certificado SSL personalizado
   - Executar configuração da linha de comando
   - Criar portais VPN e Política de Firewall
- Teste de insusimento usando Azure

## <a name="redeeming-the-fortigate-license"></a>Redentora da Licença FortiGate

O produto Fortinet FortiGate Next Generation Firewall está disponível como uma máquina virtual em Azure IaaS. Existem dois modos de licenciamento para esta máquina virtual –

- Pay as you go (PAYG)
- Traga a sua própria licença (BYOL)

Enquanto faz parceria com a Fortinet para fornecer orientação secure Hybrid Access (SHA), a Fortinet pode fornecer aos membros da equipa Azure AD Get to Production SHA com licenças. Nos casos em que não tenha sido fornecida qualquer licença, a implantação do PAYG também funcionará.

Nos casos em que tenha sido emitida uma licença, a Fortinet fornece um código de registo que deve ser resgatado online

![Fortigate SSL VPN](registration-code.png)

1. Registar-se em https://support.fortinet.com/
2. Após a inscrição, inscreva-se em https://support.fortinet.com/
3. Navegar para **o Ativo** - **> Registar/Ativar**
4. Introduza o Código de Registo fornecido por Fortinet
5. Especificar o código de registo, selecionar **O produto será utilizado por um utilizador não governamental** e clique em **Seguinte**
6. Introduza uma Descrição do Produto (por exemplo, FortiGate), defina o Parceiro Fortinet como **Outro** - > **Microsoft** e clique em **Seguinte**
7. Aceite o **Acordo de Registo do Produto Fortinet** e clique em **Seguinte**
8. Aceite os **Termos** e clique em **Confirmar**
9. Clique no Download do **Ficheiro de Licença** e guarde a licença para mais tarde


## <a name="download-firmware"></a>Baixar Firmware

No momento da escrita, o Fortinet FortiGate Azure VM não envia com a versão firmware necessária para a autenticação SAML. A versão mais recente deve ser obtida a partir de Fortinet.

1. S-in em https://support.fortinet.com/
2. Navegue para **baixar** - > **Imagens de Firmware**
3. Clique em **Baixar** para o direito de **ver notas** de lançamento
4. Clique **em v6.**
5. Clique **6.**
6. Clique **em 6.4.**
7. Baixar **FGT_VM64_AZURE-v6-build1637-FORTINET.out** clicando no link **HTTPS** na mesma linha
8. Guarde o ficheiro para mais tarde


## <a name="deploy-the-fortigate-vm"></a>Implementar o VM FortiGate

1. Navegue https://portal.azure.com e inscreva-se na subscrição na qual deseja implementar a Máquina Virtual FortiGate
2. Crie um novo Grupo de Recursos ou abra o Grupo de Recursos no qual pretende implantar a Máquina Virtual FortiGate
3. Clique **em Adicionar**
4. Introduza "Forti" no diálogo **De Pesquisa do Mercado** e selecione **Fortinet FortiGate Next Generation** **Firewall**
5. Selecione o plano de software (BYOL se tiver uma licença ou PAYG se não tiver) e clique em **Criar**
6. Povoar a configuração VM

    ![Fortigate SSL VPN](virtual-machine.png)

7. Desajuste o tipo de autenticação para **palavra-passe** e forneça credenciais administrativas para o VM
8. Clique **em Comentário + Criar**
9. Clique em **Criar**
10. Aguarde que a implantação em VM esteja concluída


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Desconfie de um endereço IP público Statuc e atribua um nome de domínio totalmente qualificado

Para uma experiência de utilizador consistente, é desejável definir o endereço IP público atribuído ao VM FortiGate para ser atribuído estáticamente. Além disso, mapeá-lo para um nome de domínio totalmente qualificado também é útil pelas mesmas razões.

_Definir um endereço IP público estático_

1. Navegue https://portal.azure.com para abrir as definições para o VM FortiGate
2. No ecrã **geral,** clique no endereço IP público

    ![Fortigate SSL VPN](public-ip-address.png)

3. Clique **em Estática** e, em seguida, clique em **Guardar**

_Atribuir um nome de domínio totalmente qualificado_

Se possuir um nome de domínio publicamente rotaível para o ambiente em que o VM FortiGate está a ser implantado, crie um registo de Anfitrião (A) para o VM que mapeie para o endereço IP público que está estaticamente atribuído acima.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Criar uma nova regra do grupo de segurança da rede de entrada para a porta TCP

1. Navegue https://portal.azure.com para abrir as definições para o VM FortiGate
2. Clique em **Networking** no menu da mão esquerda. A interface de rede será listada e as regras de porta de entrada exibidas
3. Clique **Em Adicionar a regra da porta de entrada**
4. Criar uma nova regra portuária de entrada para TCP 8443

    ![Fortigate SSL VPN](port-rule.png)

5. Clique **em Adicionar**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Crie uma app Azure personalizada para FortiGate

1. Navegue https://portal.azure.com para abrir e abra a lâmina do Diretório Ativo Azure para o inquilino que fornecerá identidade para os insus de fortiGate
2. Clique em **Aplicações Empresariais** no menu à esquerda
3. Clique **em Nova Aplicação**
4. Clique **na aplicação não-galeria**
5. Fornecer um nome (por exemplo, FortiGate) e clique em **Adicionar**
6. Clique em **Utilizadores e grupos** no menu à esquerda
7. Adicionar utilizadores que serão capazes de iniciar sessão e clicar **Em Atribuir**
8. Clique **em 'Único's no** menu da mão esquerda
9. Clique **em SAML**
10. Na **Configuração Básica SAML** clique no lápis para editar a configuração
11. Configurar
    - Identificador (ID de entidade) a ser `https://<address>/remote/saml/metadata`
    - URL de resposta (URL de serviço ao consumidor de afirmação) a ser  `https://<address>/remote/saml/login`
    - URL logout para ser `https://<address>/remote/saml/logout`

    Onde `address` está o FQDN ou o endereço IP público atribuído ao LM FortiGate

    Grave cada um destes URLs para utilização posterior –

    - ID de entidade
    - URL de Resposta
    - Logout URL
12. Clicar em **Guardar**
13. Feche a Configuração Básica SAML
14. Sub **3 – Certificado de Assinatura SAML,** faça o download do **Certificado (Base64)** e guarde-o para mais tarde
15. Abaixo **de 4 – Configurar (Nome da App)** , copiar o URL de Login Azure, O Identificador AD AD e URL Azure Logout e guardá-los para mais tarde
    - Azure Login URL
    - Identificador de Azure Ad
    - Azure Logout URL
16. Abaixo **de 2 – Atributos e Reclamações do Utilizador,** clique no lápis para editar a configuração
17. Clique **Em Adicionar nova reclamação**
18. Desabine o nome de **utilizador**
19. Desabine o atributo Fonte ao **nome user.userprincipal**
20. Clicar em **Guardar**
21. Clique **em Adicionar uma reivindicação de grupo**
22. Selecione **Todos os grupos**
23. Verifique **personalizar o nome da reclamação do grupo**
24. Definir o Nome para **grupo**
25. Clicar em **Guardar**


## <a name="prepare-for-group-matching"></a>Preparar para a correspondência do grupo

O FortiGate permite diferentes experiências no portal do utilizador após a sindução com base na adesão ao grupo. Por exemplo, pode haver uma experiência para o grupo de Marketing e outra para o grupo financeiro.

Configure isto da seguinte forma –

### <a name="create-groups-for-users"></a>Criar Grupos para Utilizadores

1. Navegue https://portal.azure.com para abrir e abra a lâmina do Diretório Ativo Azure para o inquilino que fornecerá identidade para os insus de fortiGate
2. Clique **em Grupos**
3. Clique **em Novo Grupo**
4. Criar um grupo com
    - Tipo de grupo = Segurança
    - Nome do grupo = `a meaningful name`
    - Descrição do grupo = `a meaningful description for the group`
    - Tipo de adesão = Atribuído
    - Membros = `users for the user experience that will map to this group`
5. Repita os passos 3 e 4 para quaisquer experiências adicionais do utilizador
6. Após a criação dos grupos, selecione cada grupo e grave o ID do objeto para cada um
7. Guarde estes Ids de objeto e nomes de grupo para mais tarde


## <a name="configure-the-fortigate-vm"></a>Configure o VM FortiGate

### <a name="install-the-license"></a>Instalar a Licença

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Continue para além de quaisquer erros de certificado
3. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
4. Se a implementação utilizar o modelo BYOL, será mostrado um pedido para carregar uma licença. Selecione o ficheiro de licença criado anteriormente e carreque-o, clique **em OK** e reinicie o VM FortiGate –

    ![Fortigate SSL VPN](license.png)

5. Após o reboot, inscreva-se novamente com as credenciais do administrador para validar a licença

### <a name="update-firmware"></a>Atualizar Firmware

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Continue para além de quaisquer erros de certificado
3. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
4. No menu da mão esquerda, clique no **Sistema**
5. No menu à esquerda no Sistema, clique em **Firmware**
6. Na página De Gestão de Firmware, clique em **Procurar** e selecione o ficheiro firmware descarregado anteriormente
7. Ignore o aviso e clique **em Backup config e upgrade** –

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. Clique **em Continuar**
9. Quando solicitado para guardar a configuração do FortiGate (como um ficheiro .conf), clique em **Guardar**
10. Aguarde que o firmware seja carregado, para que seja aplicado e para que o VM fortiGate reinicie
11. Após o Reinício do LM FortiGate, inscreva-se novamente com as credenciais do administrador
12. Quando solicitado para executar a configuração do dashboard, clique mais **tarde**
13. Quando o vídeo tutorial começar, clique em **OK**

### <a name="change-the-management-port-to-tcp"></a>Alterar o Porto de Gestão para TCP

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Continue para além de quaisquer erros de certificado
3. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
4. No menu da mão esquerda, clique no **Sistema**
5. Em Definições de Administração, altere a porta HTTPS para **8443**
6. Clique **em Aplicar**
7. Após a aplicação da alteração, o navegador tentará recarregar a página De Administração, mas falhará. A partir de agora, o endereço da página da administração será `https://<address>`

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Faça o upload do Certificado de Assinatura SAML do Diretório Ativo Azure

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Continue para além de quaisquer erros de certificado
3. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
4. No menu da mão esquerda, clique no **Sistema**
5. Em Sistema, clique em **Certificados**
6. Clique **em Importar** - > Certificado **Remoto**
7. Navegue para o certificado descarregado a partir da implementação de App personalizada FortiGate no inquilino Azure, selecione-o e clique **em OK**

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Upload e Configurar um Certificado SSL personalizado

Pode desejar configurar o FM FortiGate com o seu próprio certificado SSL que suporta o FQDN que está a utilizar. Se tiver acesso a um certificado SSL embalado com a chave privada em . Formato PFX, pode ser usado para este fim

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Continue para além de quaisquer erros de certificado
3. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
4. No menu da mão esquerda, clique no **Sistema**
5. Em Sistema, clique em **Certificados**
6. Clique **em Importar** - > Certificado **Local**
7. Clique no **Certificado de #12 PKCS**
8. Navegue pelo . Ficheiro PFX que contém o Certificado SSL e a Chave Privada
9. Providenciar o . Senha PFX
10. Fornecer um nome significativo para o Certificado
11. Clique **em OK**
12. No menu da mão esquerda, clique no **Sistema**
13. No Sistema, clique em **Definições**
14. Em Definições de Administração, expanda a queda ao lado do certificado do servidor HTTPS e selecione o certificado SSL importado acima
15. Clique **em Aplicar**
16. Feche a janela do navegador e, em seguida, navegue novamente para `https://<address>`
17. Iniciar s-in com as credenciais de administrador do FortiGate e observar o certificado SSL correto em uso


### <a name="perform-command-line-configuration"></a>Executar configuração da linha de comando

_Executar a configuração da linha de comando para autenticação SAML_

1. Navegue https://portal.azure.com para abrir as definições para o VM FortiGate
2. No menu da mão esquerda, clique na **Consola Em Série**
3. Iniciar s-in na Consola em Série com as credenciais de administrador do FortiGate VM

    Para o próximo passo, os URLs registados anteriormente serão necessários. Nomeadamente –

    - ID de entidade
    - URL de Resposta
    - Logout URL
    - Azure Login URL
    - Identificador de Azure Ad
    - Azure Logout URL
4. Na Consola em Série, execute os seguintes comandos –

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
    > O URL Azure Logout contém um? personagem. Isto requer uma sequência de chave especial para que seja fornecida corretamente à Consola em Série FortiGate. O URL é tipicamente...

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Para fornecer isto na Consola em Série, proceda digitando

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Em seguida, digite CTRL+V,

    Em seguida, cole o resto do URL para completar a linha

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Para confirmar a configuração, execute -

    ```
    show user saml
    ```

_Executar a configuração da linha de comando para a correspondência de grupo_

1. Navegue https://portal.azure.com para abrir as definições para o VM FortiGate
2. No menu da mão esquerda, clique na **Consola Em Série**
3. Iniciar s-in na Consola em Série com as credenciais de administrador do FortiGate VM
4. Na Consola em Série, execute os seguintes comandos –

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

    Repita estes comandos de edição `group 1 name` mas para cada grupo adicional que terá uma experiência de portal diferente no FortiGate

_Executar a configuração da linha de comando para a autenticação time out_

1. Navegue https://portal.azure.com para abrir as definições para o VM FortiGate
2. No menu da mão esquerda, clique na **Consola Em Série**
3. Iniciar s-in na Consola em Série com as credenciais de administrador do FortiGate VM
4. Na Consola em Série, execute os seguintes comandos –

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Criar portais VPN e Política de Firewall

1. Navegue para `https://<address>`

    aqui `address` está o FQDN ou o endereço IP público atribuído ao FM FortiGate

2. Iniciar sação utilizando as credenciais de administrador fornecidas durante a implantação do FortiGate VM
3. No menu da esquerda, clique em **VPN**
4. Em VPN, clique em **Portais SSL-VPN**
5. Clique **em Criar Novo**
6. Fornecer um nome (geralmente combinando-o com o Grupo Azure usado para fornecer a experiência personalizada do portal)
7. Clique no sinal mais **+** () ao lado de Pools IP Fonte, selecione a piscina predefinitiva e clique em **Fechar**
8. Personalize a experiência para este grupo. Para testes, esta pode ser a personalização da Mensagem do Portal e do Tema. É também aqui que pode criar marcadores personalizados que direcionam os utilizadores para recursos internos
9. Clique **em OK**
10. Repita os passos 5 a 9 para cada Grupo Azure que terá uma experiência personalizada no portal
11. Em VPN, clique em **Definições SSL-VPN**
12. Clique no sinal mais **+** () ao lado de Ouvir em Interfaces
13. Selecione **Port1** e clique **em Fechar**


14. Se um certificado SSL personalizado tiver sido previamente instalado, altere o Certificado de Servidor para utilizar o certificado SSL personalizado no menu suspenso
15. Em Autenticação/Mapeamento do Portal, clique em **Criar Novo**
16. Escolha o primeiro Grupo Azure e combine-o com o Portal do mesmo nome
17. Clique **em OK**
18. Repita os passos 15 a 17 para cada par Azure Group / Portal
19. Em Autenticação/Mapeamento do Portal, edite **todos os outros utilizadores/grupos**
20. Definir o portal para **o acesso total**
21. Clique **em OK**
22. Clique **em Aplicar**
23. Percorra o topo da página de Definição SSL-VPN e clique no aviso de que não existem **políticas SSL-VPN.** 
     ** Clique aqui para criar uma nova política SSL-VPN usando estas definições**
24. Fornecer um nome como **VPN Grp**
25. Definir Interface de saída **para** porta
26. Clique **em Origem**
27. Em Endereço, selecione **todos**
28. Sob Utilizador, selecione o primeiro Grupo Azure
29. Clique **perto**
30. Clique **no destino**
31. Em Address, esta seria geralmente a rede interna. Selecione login.microsoft.com para testes
32. Clique **perto**
33. **Clique em Serviço**
34. Clique **em Tudo**
35. Clique **perto**
36. Clique **em OK**
37. No menu da esquerda, clique em **Política & Objetos**
38. Em "Objetos de & de Política", clique na **Política de Firewall**
39. Expandir **a interface do túnel SSL-VPN (ssl.root) -> porta**
40. Clique com o botão direito na política VPN criada anteriormente **(VPN Grp 1** ) e selecione **Copy**
41. Clique com o botão direito sob a política VPN e **selecione Pasta** - > **Abaixo**
42. Editar a nova política, fornecendo-lhe um nome diferente (por exemplo **VPN Grp2** ) e mudar o grupo é aplicável (outro Grupo Azure)
43. Clique com o direito na nova política e dese couse o estado de **Ativação**


## <a name="test-sign-in-using-azure"></a>Teste de insusimento usando Azure

1. Usando uma sessão de navegador em privado, navegue para `https://<address>` 
2. O sign-in deve redirecionar para o Azure Ative Directory para o sign-in
3. Depois de fornecer credenciais para um utilizador que foi designado para a App FortiGate no inquilino Azure, o portal de utilizador apropriado deve ser mostrado

    ![Fortigate SSL VPN](test-sign-in.png)
