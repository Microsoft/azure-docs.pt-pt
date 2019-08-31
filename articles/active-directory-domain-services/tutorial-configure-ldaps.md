---
title: Tutorial – configurar LDAPs para Azure Active Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar o protocolo LDAP (Lightweight Directory Access Protocol) para um domínio gerenciado Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 2eaae9093614f1512dcd75d23c98bca871bf2850
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193322"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configurar o LDAP seguro para um domínio gerenciado Azure Active Directory Domain Services

Para se comunicar com seu domínio gerenciado do Azure Active Directory Domain Services (AD DS do Azure), o protocolo LDAP é usado. Por padrão, o tráfego LDAP não é criptografado, o que é uma preocupação de segurança para muitos ambientes. Com o Azure AD DS, você pode configurar o domínio gerenciado para usar o protocolo LDAP (Lightweight Directory Access Protocol) seguro. Quando você usa o LDAP seguro, o tráfego é criptografado. LDAP Seguro também é conhecido como LDAP sobre protocolo SSL (SSL)/TLS (Transport Layer Security).

Este tutorial mostra como configurar LDAPs para um domínio gerenciado AD DS do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para uso com o Azure AD DS
> * Habilitar o LDAP seguro para o Azure AD DS
> * Configurar o LDAP seguro para uso pela Internet pública
> * Associar e testar o LDAP seguro para um domínio gerenciado do Azure AD DS

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* A ferramenta *LDP. exe* instalada no seu computador.
    * Se necessário, [Instale o ferramentas de administração de servidor remoto (RSAT)][rsat] para *Active Directory Domain Services e LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, você configura o LDAP seguro para o domínio gerenciado AD DS do Azure usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Criar um certificado para LDAP seguro

Para usar o LDAP seguro, um certificado digital é usado para criptografar a comunicação. Esse certificado digital é aplicado ao seu domínio gerenciado AD DS do Azure e permite que ferramentas como *LDP. exe* usem comunicação criptografada segura ao consultar dados. Há duas maneiras de criar um certificado para acesso LDAP seguro ao domínio gerenciado:

* Um certificado de uma autoridade de certificação pública (CA) ou uma autoridade de certificação corporativa.
    * Se sua organização obtiver certificados de uma AC pública, obtenha o certificado LDAP seguro dessa AC pública. Se você usar uma autoridade de certificação corporativa em sua organização, obtenha o certificado LDAP seguro da AC corporativa.
    * Uma CA pública só funciona quando você usa um nome DNS personalizado com seu domínio gerenciado AD DS do Azure. Se o nome de domínio DNS do domínio gerenciado terminar em *. onmicrosoft.com*, você não poderá criar um certificado digital para proteger a conexão com esse domínio padrão. A Microsoft possui o domínio *. onmicrosoft.com* , portanto, uma CA pública não emitirá um certificado. Nesse cenário, crie um certificado autoassinado e use-o para configurar o LDAP seguro.
* Um certificado autoassinado que você cria por conta própria.
    * Essa abordagem é válida para fins de teste e é o que este tutorial mostra.

O certificado solicitado ou criado deve atender aos seguintes requisitos. O domínio gerenciado encontra problemas se você habilitar o LDAP seguro com um certificado inválido:

* **Emissor confiável** -o certificado deve ser emitido por uma autoridade confiável por computadores que se conectam ao domínio gerenciado usando o LDAP seguro. Essa autoridade pode ser uma CA pública ou uma autoridade de certificação corporativa confiável para esses computadores.
* **Tempo de vida** -o certificado deve ser válido por pelo menos os próximos 3-6 meses. LDAP Seguro acesso ao domínio gerenciado é interrompido quando o certificado expira.
* **Nome da entidade** -o nome da entidade no certificado deve ser seu domínio gerenciado. Por exemplo, se seu domínio for denominado *contoso.com*, o nome da entidade do certificado deverá ser * *. contoso.com*.
    * O nome DNS ou o nome alternativo da entidade do certificado deve ser um certificado curinga para garantir que o LDAP seguro funcione corretamente com o Azure AD Domain Services. Os controladores de domínio usam nomes aleatórios e podem ser removidos ou adicionados para garantir que o serviço permaneça disponível.
* **Uso de chave** -o certificado deve ser configurado para *assinaturas digitais* e *codificação de chave*.
* **Finalidade do certificado** -o certificado deve ser válido para autenticação do servidor SSL.

Neste tutorial, vamos criar um certificado autoassinado para LDAP seguro usando o PowerShell. Abra uma janela do PowerShell como **administrador** e execute os comandos a seguir. Substitua a variável *$DnsName* pelo nome DNS usado por seu próprio domínio gerenciado, como *contoso.com*:

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="contoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A saída de exemplo a seguir mostra que o certificado foi gerado com êxito e armazenado no repositório de certificados local (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=contoso.com
```

## <a name="understand-and-export-required-certificates"></a>Entender e exportar os certificados necessários

Para usar o LDAP seguro, o tráfego de rede é criptografado usando a PKI (infraestrutura de chave pública).

* Uma chave **privada** é aplicada ao domínio gerenciado AD DS do Azure.
    * Essa chave privada é usada para descriptografar o tráfego LDAP seguro. A chave privada só deve ser aplicada ao domínio gerenciado AD DS do Azure e não amplamente distribuída a computadores cliente.
    * Um certificado que inclui a chave privada usa o *.* Formato de arquivo PFX.
* Uma chave **pública** é aplicada aos computadores cliente.
    * Essa chave pública é usada para *criptografar* o tráfego LDAP seguro. A chave pública pode ser distribuída para computadores cliente.
    * Os certificados sem a chave privada usam o *.* Formato de arquivo cer.

Essas duas chaves, as chaves *pública* e *privada* , garantem que apenas os computadores apropriados possam se comunicar com êxito entre si. Se você usar uma AC pública ou uma AC corporativa, será emitido com um certificado que inclui a chave privada e pode ser aplicado a um domínio gerenciado do Azure AD DS. A chave pública já deve ser conhecida e confiável por computadores cliente. Neste tutorial, você criou um certificado autoassinado com a chave privada, portanto, você precisa exportar os componentes públicos e privados apropriados.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportar um certificado para o Azure AD DS

Antes de poder usar o certificado digital criado na etapa anterior com seu domínio gerenciado AD DS do Azure, exporte o certificado para um *.* Arquivo de certificado PFX que inclui a chave privada.

1. Para abrir a caixa de diálogo *executar* , selecione as chaves **Windows** e **R** .
1. Abra o MMC (console de gerenciamento Microsoft) inserindo **MMC** na caixa de diálogo *executar* e selecione **OK**.
1. No prompt de **controle de conta de usuário** , clique em **Sim** para iniciar o MMC como administrador.
1. No menu **arquivo** , clique em **Adicionar/remover snap-in...**
1. No assistente de **snap-in de certificados** , escolha **conta de computador**e, em seguida, selecione **Avançar**.
1. Na página **Selecionar computador** , escolha **computador local: (o computador no qual este console está sendo executado)** e, em seguida, selecione **concluir**.
1. Na caixa de diálogo **Adicionar ou remover snap-ins** , clique em **OK** para adicionar o snap-in de certificados ao MMC.
1. Na janela do MMC, expanda **raiz do console**. Selecione **certificados (computador local)** e, em seguida, expanda o nó **pessoal** , seguido pelo nó **certificados** .

    ![Abrir o repositório de certificados pessoais no console de gerenciamento Microsoft](./media/tutorial-configure-ldaps/open-personal-store.png)

1. O certificado autoassinado criado na etapa anterior é mostrado, como *contoso.com*. Selecione o certificado e escolha **todas as tarefas > exportar...**

    ![Exportar certificado no console de gerenciamento Microsoft](./media/tutorial-configure-ldaps/export-cert.png)

1. No **Assistente para exportação de certificados**, selecione **Avançar**.
1. A chave privada do certificado deve ser exportada. Se a chave privada não estiver incluída no certificado exportado, a ação para habilitar o LDAP seguro para o domínio gerenciado falhará.

    Na página **Exportar chave privada** , escolha **Sim, exportar a chave privada**e, em seguida, selecione **Avançar**.
1. Os domínios gerenciados AD DS do Azure só dão suporte ao *.* Formato de arquivo de certificado PFX que inclui a chave privada. Não exporte o certificado como *.* Formato de arquivo de certificado cer sem a chave privada.

    Na página **formato do arquivo de exportação** , selecione **troca de informações pessoais – #12 PKCS (. PFX)** como o formato de arquivo para o certificado exportado. Se possível, marque a caixa *incluir todos os certificados no caminho de certificação*:

    ![Escolha a opção para exportar o certificado no PKCS 12 (. PFX) formato de arquivo](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Como esse certificado é usado para descriptografar dados, você deve controlar cuidadosamente o acesso. Uma senha pode ser usada para proteger o uso do certificado. Sem a senha correta, o certificado não pode ser aplicado a um serviço.

    Na página **segurança** , escolha a opção de **senha** para proteger o *.* Arquivo de certificado PFX. Insira e confirme uma senha e, em seguida, selecione **Avançar**. Essa senha é usada na próxima seção para habilitar o LDAP seguro para seu domínio gerenciado AD DS do Azure.
1. Na página **arquivo a ser** exportado, especifique o nome do arquivo e o local onde você gostaria de exportar o certificado, como *C:\Users\accountname\azure-AD-DS.pfx*.
1. Na página revisão, selecione **concluir** para exportar o certificado para um *.* Arquivo de certificado PFX. Uma caixa de diálogo de confirmação é exibida quando o certificado foi exportado com êxito.
1. Deixe o MMC aberto para uso na seção a seguir.

### <a name="export-a-certificate-for-client-computers"></a>Exportar um certificado para computadores cliente

Os computadores cliente devem confiar no emissor do certificado LDAP seguro para poder se conectar com êxito ao domínio gerenciado usando LDAPs. Os computadores cliente precisam de um certificado para criptografar com êxito os dados descriptografados pelo Azure AD DS. Se você usar uma AC pública, o computador deverá confiar automaticamente nesses emissores de certificado e ter um certificado correspondente. Neste tutorial, você usa um certificado autoassinado e gerou um certificado que inclui a chave privada na etapa anterior. Agora, vamos exportar e, em seguida, instalar o certificado autoassinado no repositório de certificados confiáveis no computador cliente:

1. Volte para o MMC para *certificados (computador local) > armazenamento de certificados pessoais >* . O certificado autoassinado criado em uma etapa anterior é mostrado, como *contoso.com*. Selecione o certificado e escolha **todas as tarefas > exportar...**
1. No **Assistente para exportação de certificados**, selecione **Avançar**.
1. Como você não precisa da chave privada para clientes, na página **Exportar chave privada** , escolha não **, não exportar a chave privada**e, em seguida, selecione **Avançar**.
1. Na página **formato do arquivo de exportação** , selecione **X. 509 codificado em base-64 (. CER)** como o formato de arquivo para o certificado exportado:

    ![Escolha a opção para exportar o certificado no X. 509 codificado em base-64 (. CER) formato de arquivo](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na página **arquivo a ser** exportado, especifique o nome do arquivo e o local onde você gostaria de exportar o certificado, como *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Na página revisão, selecione **concluir** para exportar o certificado para um *.* Arquivo de certificado cer. Uma caixa de diálogo de confirmação é exibida quando o certificado foi exportado com êxito.

O *.* O arquivo de certificado cer agora pode ser distribuído para computadores cliente que precisam confiar na conexão LDAP segura para o domínio gerenciado AD DS do Azure. Vamos instalar o certificado no computador local.

1. Abra o explorador de arquivos e navegue até o local onde você salvou o *.* Arquivo de certificado cer, como *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Selecione o botão direito do mouse *.* Arquivo de certificado cer e escolha **Instalar certificado**.
1. No **Assistente para importação de certificados**, escolha armazenar o certificado no *computador local*e, em seguida, selecione **Avançar**:

    ![Escolha a opção para importar o certificado para o repositório do computador local](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quando solicitado, escolha **Sim** para permitir que o computador faça alterações.
1. Escolha **selecionar automaticamente o repositório de certificados com base no tipo de certificado**e, em seguida, selecione **Avançar**.
1. Na página revisão, selecione **concluir** para importar o *. Certificado CER* . arquivo uma caixa de diálogo de confirmação é exibida quando o certificado foi importado com êxito.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Habilitar o LDAP seguro para o Azure AD DS

Com um certificado digital criado e exportado que inclui a chave privada e o computador cliente definido para confiar na conexão, agora habilite o LDAP seguro em seu domínio gerenciado AD DS do Azure. Para habilitar o LDAP seguro em um domínio gerenciado AD DS do Azure, execute as seguintes etapas de configuração:

1. Na [portal do Azure](https://portal.azure.com), procure serviços de *domínio* na caixa **Pesquisar recursos** . Selecione **Azure AD Domain Services** no resultado da pesquisa.

    ![Pesquise e selecione seu domínio gerenciado AD DS do Azure no portal do Azure](./media/tutorial-configure-ldaps/search-for-domain-services.png)

1. Escolha seu domínio gerenciado, como *contoso.com*.
1. No lado esquerdo da janela de AD DS do Azure, escolha **LDAP seguro**.
1. Por padrão, o acesso LDAP seguro ao domínio gerenciado é desabilitado. Alterne **LDAP seguro** para **habilitar**.
1. O acesso LDAP Seguro ao domínio gerenciado pela Internet é desabilitado por padrão. Quando você habilita o acesso LDAP seguro ao público, seu domínio é suscetível a ataques de força bruta de senha pela Internet. Na próxima etapa, um grupo de segurança de rede é configurado para bloquear o acesso somente aos intervalos de endereços IP de origem necessários.

    Alterne **permitir acesso LDAP seguro pela Internet** para **habilitar**.

1. Selecione o ícone de pasta ao lado de **. Arquivo PFX com certificado LDAP seguro**. Navegue até o caminho do *. Arquivo PFX* e, em seguida, selecione o certificado criado em uma etapa anterior que inclui a chave privada.

    Conforme observado na seção anterior sobre requisitos de certificado, você não pode usar um certificado de uma AC pública com o domínio default *. onmicrosoft.com* . A Microsoft possui o domínio *. onmicrosoft.com* , portanto, uma CA pública não emitirá um certificado. Verifique se o certificado está no formato apropriado. Se não for, a plataforma Azure gerará erros de validação de certificado quando você habilitar o LDAP seguro.

1. Digite a **senha a ser descriptografada. Arquivo PFX** definido em uma etapa anterior quando o certificado foi exportado para um *. Arquivo PFX* .
1. Selecione **salvar** para habilitar o LDAP seguro.

    ![Habilitar o LDAP seguro para um domínio gerenciado do Azure AD DS no portal do Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

É exibida uma notificação de que o LDAP seguro está sendo configurado para o domínio gerenciado. Você não pode modificar outras configurações para o domínio gerenciado até que essa operação seja concluída.

Leva alguns minutos para habilitar o LDAP seguro para seu domínio gerenciado. Se o certificado LDAP seguro fornecido não corresponder aos critérios necessários, a ação para habilitar o LDAP seguro para o domínio gerenciado falhará. Alguns motivos comuns de falha são se o nome de domínio estiver incorreto ou se o certificado expirar em breve ou se já tiver expirado. Você pode recriar o certificado com parâmetros válidos e, em seguida, habilitar o LDAP seguro usando esse certificado atualizado.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Bloquear o acesso LDAP seguro pela Internet

Quando você habilita o acesso LDAP seguro pela Internet para seu domínio gerenciado AD DS do Azure, ele cria uma ameaça à segurança. O domínio gerenciado pode ser acessado pela Internet na porta TCP 636. É recomendável restringir o acesso ao domínio gerenciado para endereços IP conhecidos específicos para seu ambiente. Uma regra de grupo de segurança de rede do Azure pode ser usada para limitar o acesso ao LDAP seguro.

Vamos criar uma regra para permitir o acesso LDAP seguro de entrada pela porta TCP 636 de um conjunto especificado de endereços IP. Uma regra *denyall* padrão com prioridade mais baixa se aplica a todos os outros tráfegos de entrada da Internet, de modo que apenas os endereços especificados possam acessar o domínio gerenciado do Azure AD DS usando o LDAP seguro.

1. No portal do Azure, selecione *grupos de recursos* na navegação do lado esquerdo.
1. Escolha o grupo de recursos, como *MyResource*Group e, em seguida, selecione o grupo de segurança de rede, como *AADDS-contoso.com-NSG*.
1. A lista de regras de segurança de entrada e saída existentes é exibida. No lado esquerdo das janelas do grupo de segurança de rede, escolha **segurança > regras de segurança de entrada**.
1. Selecione **Adicionar**e crie uma regra para permitir a porta TCP *636*. Para maior segurança, escolha a origem como *endereços IP* e, em seguida, especifique seu próprio endereço IP válido ou intervalo para sua organização.

    | Definição                           | Value        |
    |-----------------------------------|--------------|
    | Source                            | Endereços IP |
    | Intervalos de CIDR/endereços IP de origem | Um endereço IP válido ou um intervalo para o seu ambiente |
    | Source port ranges                | *            |
    | Destino                       | Any          |
    | Intervalos de portas de destino           | 636          |
    | Protocol                          | TCP          |
    | Action                            | Allow        |
    | Priority                          | 401          |
    | Name                              | AllowLDAPS   |

1. Quando estiver pronto, selecione **Adicionar** para salvar e aplicar a regra.

    ![Criar uma regra de grupo de segurança de rede para proteger o acesso LDAPs pela Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configurar a zona DNS para acesso externo

Com o acesso LDAP seguro habilitado pela Internet, atualize a zona DNS para que os computadores cliente possam encontrar esse domínio gerenciado. O *endereço IP externo LDAP seguro* está listado na guia **Propriedades** do seu domínio gerenciado do Azure AD DS:

![Exiba o endereço IP externo de LDAP seguro para seu domínio gerenciado do Azure AD DS no portal do Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configure seu provedor DNS externo para criar um registro de host, como *LDAPS*, para resolver esse endereço IP externo. Para testar localmente em seu computador primeiro, você pode criar uma entrada no arquivo hosts do Windows. Para editar com êxito o arquivo de hosts em seu computador local, abra o *bloco de notas* como administrador e, em seguida, abra o arquivo *c:\WINDOWS\system32\drivers\etc.*

O exemplo de entrada DNS a seguir, com seu provedor DNS externo ou no arquivo hosts local, resolve o tráfego para *LDAPS.contoso.com* para o endereço IP externo de *40.121.19.239*:

```
40.121.19.239    ldaps.contoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testar consultas para o domínio gerenciado

Para se conectar e associar-se ao domínio gerenciado AD DS do Azure e Pesquisar por LDAP, use também o *LDP. exe* . Essa ferramenta está incluída no pacote do Ferramentas de Administração de Servidor Remoto (RSAT). Para obter mais informações, consulte [install ferramentas de administração de servidor remoto][rsat].

1. Abra o *LDP. exe* e conecte-se ao domínio gerenciado. Selecione **conexão**e, em seguida, escolha **conectar...** .
1. Insira o nome de domínio DNS seguro do domínio gerenciado criado na etapa anterior, como *LDAPS.contoso.com*. Para usar o LDAP seguro, defina **porta** como *636*e marque a caixa para **SSL**.
1. Selecione **OK** para se conectar ao domínio gerenciado.

Em seguida, associe-se ao seu domínio gerenciado AD DS do Azure. Os usuários (e as contas de serviço) não poderão executar associações LDAP simples se você tiver desabilitado a sincronização de hash de senha NTLM na instância de AD DS do Azure. Para obter mais informações sobre como desabilitar a sincronização de hash de senha NTLM, consulte [proteger seu domínio gerenciado AD DS do Azure][secure-domain].

1. Selecione a opção de menu **conexão** e, em seguida, escolha **associar...** .
1. Forneça as credenciais de uma conta de usuário pertencente ao grupo de *Administradores de DC do AAD* , como *contosoadmin*. Insira a senha da conta do usuário e, em seguida, insira seu domínio, como *contoso.com*.
1. Para **tipo de associação**, escolha a opção para *associar com credenciais*.
1. Selecione **OK** para associar ao seu domínio gerenciado AD DS do Azure.

Para ver os objetos armazenados em seu domínio gerenciado AD DS do Azure:

1. Selecione a opção de menu **Exibir** e, em seguida, escolha **árvore**.
1. Deixe o campo *baseado* em branco e selecione **OK**.
1. Escolha um contêiner, como *usuários AADDC*, selecione o contêiner com o botão direito do mouse e escolha **Pesquisar**.
1. Deixe os campos preenchidos previamente definidos e, em seguida, selecione **executar**. Os resultados da consulta são mostrados na janela à direita.

    ![Pesquisar objetos em seu domínio gerenciado AD DS do Azure usando LDP. exe](./media/tutorial-configure-ldaps/ldp-query.png)

Para consultar diretamente um contêiner específico, no menu de **árvore exibir >** , você pode especificar um **com base** , como ou *= AADDC usuários, DC = contoso, DC = com* ou *= AADDC Computers, DC = contoso, DC = com*. Para obter mais informações sobre como formatar e criar consultas, consulte [noções básicas de consulta LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Limpar recursos

Se você adicionou uma entrada DNS ao arquivo hosts local do seu computador para testar a conectividade para este tutorial, remova essa entrada e adicione um registro formal em sua zona DNS. Para remover a entrada do arquivo hosts local, conclua as seguintes etapas:

1. No computador local, abra o *bloco de notas* como administrador
1. Navegue até e abra o arquivo *c:\WINDOWS\system32\drivers\etc.*
1. Exclua a linha do registro que você adicionou, como`40.121.19.239    ldaps.contoso.com`

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para uso com o Azure AD DS
> * Habilitar o LDAP seguro para o Azure AD DS
> * Configurar o LDAP seguro para uso pela Internet pública
> * Associar e testar o LDAP seguro para um domínio gerenciado do Azure AD DS

> [!div class="nextstepaction"]
> [Configurar a sincronização de hash de senha para um ambiente híbrido do Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
