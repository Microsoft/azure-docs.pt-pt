---
title: Tutorial - Configurar LDAPS para Azure Ative Directory Domain Services | Microsoft Docs
description: Neste tutorial, você aprende a configurar o protocolo de acesso de diretório leve seguro (LDAPS) para um domínio gerido Azure Ative Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: justinha
ms.openlocfilehash: 1619622ad9594f252c3d4cf5551704c6a788f9f8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564089"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configurar LDAP seguro para um domínio gerido por Azure Ative Directory Domain Services

Para comunicar com o seu domínio gerido Azure Ative Directory Domain Services (Azure AD DS), é utilizado o Protocolo de Acesso ao Diretório Leve (LDAP). Por padrão, o tráfego LDAP não é encriptado, o que é uma preocupação de segurança para muitos ambientes.

Com o Azure AD DS, pode configurar o domínio gerido para utilizar o Protocolo de Acesso ao Diretório Leve (LDAPS). Quando utilizar LDAP seguro, o tráfego é encriptado. Secure LDAP também é conhecido como LDAP sobre Camada de Tomadas Seguras (SSL) / Segurança da Camada de Transporte (TLS).

Este tutorial mostra-lhe como configurar LDAPS para um domínio gerido AZure AD DS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para uso com Azure AD DS
> * Ativar LDAP seguro para Azure AD DS
> * Configurar LDAP seguro para uso através da internet pública
> * Ligar e testar secure LDAP para um domínio gerido

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].
* A *ferramentaLDP.exe* instalada no seu computador.
    * Se necessário, [instale as Ferramentas de Administração do Servidor Remoto (RSAT)][rsat] para *Serviços de Domínio de Diretório Ativo e LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, configura lDAP seguro para o domínio gerido usando o portal Azure. Para começar, inicie primeiro o sinal no [portal Azure.](https://portal.azure.com)

## <a name="create-a-certificate-for-secure-ldap"></a>Criar um certificado para LDAP seguro

Para utilizar lDAP seguro, um certificado digital é usado para encriptar a comunicação. Este certificado digital é aplicado ao seu domínio gerido e permite que ferramentas como *LDP.exe* utilizem comunicação encriptada segura ao consultar dados. Existem duas formas de criar um certificado para acesso LDAP seguro ao domínio gerido:

* Certificado de uma autoridade de certificados públicos (CA) ou de uma empresa ca.
    * Se a sua organização obtiver certificados de um CA público, obtenha o certificado LDAP seguro da AC pública. Se utilizar uma EMPRESA CA na sua organização, obtenha o certificado LDAP seguro da empresa CA.
    * Um CA público só funciona quando utiliza um nome DNS personalizado com o seu domínio gerido. Se o nome de domínio DNS do seu domínio gerido terminar em *.onmicrosoft.com,* não é possível criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft é dona do domínio *.onmicrosoft.com,* por isso um CA público não emitirá um certificado. Neste cenário, crie um certificado auto-assinado e use-o para configurar lDAP seguro.
* Um certificado auto-assinado que cria a si mesmo.
    * Esta abordagem é boa para fins de teste, e é o que este tutorial mostra.

O certificado que solicitar ou criar deve satisfazer os seguintes requisitos. O seu domínio gerido encontra problemas se ativar lDAP seguro com um certificado inválido:

* **Emitente fidedigno** - O certificado deve ser emitido por uma autoridade fidedigna por computadores que se ligam ao domínio gerido utilizando LDAP seguro. Esta autoridade pode ser uma AC pública ou uma AC da Enterprise confiável por estes computadores.
* **Vida útil** - O certificado deve ser válido pelo menos durante os próximos 3-6 meses. O acesso seguro do LDAP ao seu domínio gerido é interrompido quando o certificado expira.
* **Nome do assunto** - O nome do sujeito no certificado deve ser o seu domínio gerido. Por exemplo, se o seu domínio for nomeado *aaddscontoso.com,* o nome do certificado deve ser **.aaddscontoso.com*.
    * O nome DNS ou nome alternativo do certificado deve ser um certificado wildcard para garantir que o LDAP seguro funciona corretamente com os Serviços de Domínio AD AZure. Os controladores de domínio usam nomes aleatórios e podem ser removidos ou adicionados para garantir que o serviço permanece disponível.
* **Utilização chave** - O certificado deve ser configurado para *assinaturas digitais* e *ciframento de chaves*.
* **Finalidade do certificado** - O certificado deve ser válido para a autenticação do servidor TLS.

Existem várias ferramentas disponíveis para criar certificado auto-assinado como OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet, etc.

Neste tutorial, vamos criar um certificado auto-assinado para LDAP seguro usando o [cmdlet New-SelfSignedCertificate.][New-SelfSignedCertificate]

Abra uma janela PowerShell como **Administrador** e execute os seguintes comandos. Substitua a variável *$dnsName* pelo nome DNS utilizado pelo seu próprio domínio gerido, como *aaddscontoso.com:*

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A saída de exemplo a seguir mostra que o certificado foi gerado com sucesso e é armazenado na loja de certificados local *(LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Compreender e exportar certificados necessários

Para utilizar lDAP seguro, o tráfego de rede é encriptado utilizando infraestruturas de chaves públicas (PKI).

* Uma chave **privada** é aplicada ao domínio gerido.
    * Esta chave privada é usada para *desencriptar* o tráfego LDAP seguro. A chave privada só deve ser aplicada ao domínio gerido e não amplamente distribuída aos computadores clientes.
    * Um certificado que inclui a chave privada utiliza o *. Formato* de ficheiro PFX.
    * Ao exportar o certificado, deve especificar o algoritmo de encriptação *TripleDES-SHA1.* Isto aplica-se apenas ao ficheiro .pfx e não afeta o algoritmo utilizado pelo próprio certificado. Note que a opção *TripleDES-SHA1* está disponível apenas a partir do Windows Server 2016.
* Uma chave **pública** é aplicada aos computadores clientes.
    * Esta chave pública é usada para *encriptar* o tráfego LDAP seguro. A chave pública pode ser distribuída para computadores clientes.
    * Certificados sem a chave privada usam o *. Formato* de ficheiro CER.

Estas duas chaves, as chaves *privadas* e *públicas,* certificam-se de que apenas os computadores apropriados podem comunicar com sucesso uns com os outros. Se utilizar uma AC pública ou uma empresa CA, é-lhe emitido um certificado que inclui a chave privada e pode ser aplicado a um domínio gerido. A chave pública já deve ser conhecida e fidedigna pelos computadores dos clientes.

Neste tutorial, criou um certificado auto-assinado com a chave privada, pelo que precisa de exportar as componentes privadas e públicas apropriadas.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportar um certificado para Azure AD DS

Antes de utilizar o certificado digital criado no passo anterior com o seu domínio gerido, exporte o certificado para um *. Arquivo* de certificado PFX que inclui a chave privada.

1. Para abrir o diálogo *'Executar',* selecione as **teclas Windows**  +  **R.**
1. Abra a Consola de Gestão da Microsoft (MMC) introduzindo **o mmc** no diálogo *Run* e, em seguida, selecione **OK**.
1. Na solicitação **de Controlo de Conta de Utilizador,** selecione **Sim** para lançar mMC como administrador.
1. No menu **Ficheiro,** **selecione Adicionar/Remover Snap-in...**
1. No assistente **de encaixe de certificados,** escolha **a conta de Computador** e, em seguida, selecione **Seguinte**.
1. Na página **Select Computer,** escolha **computador local: (o computador em que a consola está a funcionar)** e, em seguida, selecione **Acabamento**.
1. No diálogo **Add or Remove Snap-ins,** selecione **OK** para adicionar os certificados snap-in ao MMC.
1. Na janela MMC, expanda a **raiz da consola.** Selecione **Certificados (Computador Local)**, em seguida, expanda o nó **Pessoal,** seguido do nó **certificados.**

    ![Abra a loja de certificados pessoais na Consola de Gestão da Microsoft](./media/tutorial-configure-ldaps/open-personal-store.png)

1. O certificado auto-assinado criado no escalão anterior é apresentado, como *aaddscontoso.com*. Selecione à direita este certificado e, em seguida, escolha **Todas as Tarefas > Exportar...**

    ![Certificado de exportação na Consola de Gestão da Microsoft](./media/tutorial-configure-ldaps/export-cert.png)

1. No **Certificado De Exportação de Assistente**, selecione **Seguinte**.
1. A chave privada do certificado deve ser exportada. Se a chave privada não estiver incluída no certificado exportado, a ação para permitir um LDAP seguro para o seu domínio gerido falha.

    Na página **'Chave Privada exportar',** escolha **Sim, exporte a chave privada** e, em seguida, selecione **Seguinte**.
1. Os domínios geridos suportam apenas *o . Formato* de ficheiro de certificado PFX que inclui a chave privada. Não exporte o certificado como *. Formato* de ficheiro de certificado CER sem a chave privada.

    Na página **'Formato de Ficheiros de Exportação',** selecione **Personal Information Exchange - PKCS #12 (. PFX)** como formato de ficheiro para o certificado exportado. Verifique a caixa para *incluir todos os certificados na via de certificação, se possível:*

    ![Escolha a opção de exportação do certificado no PKCS 12 (. Formato de ficheiro PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Uma vez que este certificado é utilizado para desencriptar dados, deve controlar cuidadosamente o acesso. Uma palavra-passe pode ser usada para proteger a utilização do certificado. Sem a senha correta, o certificado não pode ser aplicado a um serviço.

    Na página **'Segurança',** escolha a opção por **Palavra-Passe** para proteger o *.* Arquivo de certificado PFX. O algoritmo de encriptação deve ser *TripleDES-SHA1*. Introduza e confirme uma palavra-passe e, em seguida, selecione **Seguinte**. Esta palavra-passe é utilizada na secção seguinte para permitir um LDAP seguro para o seu domínio gerido.

    Se exportar utilizando o [cmdlet de exportação powerShell,](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate)tem de passar a bandeira *-CryptoAlgorithmOption* utilizando TripleDES_SHA1.

    ![Screenshot de como encriptar a palavra-passe](./media/tutorial-configure-ldaps/encrypt.png)

1. Na página **"Ficheiro para Exportação",** especifique o nome do ficheiro e o local onde pretende exportar o certificado, tais como *C:\Users\accountname\azure-ad-ds.pfx*. Mantenha uma nota da senha e localização do *. Ficheiro PFX,* uma vez que esta informação seria necessária nos próximos passos.
1. Na página de análise, **selecione Finish** para exportar o certificado para um *.* Arquivo de certificado PFX. É apresentado um diálogo de confirmação quando o certificado foi exportado com sucesso.
1. Deixe o MMC aberto para utilização na secção seguinte.

### <a name="export-a-certificate-for-client-computers"></a>Exportar um certificado para computadores clientes

Os computadores clientes devem confiar no emitente do certificado LDAP seguro para poderem ligar-se com sucesso ao domínio gerido utilizando LDAPS. Os computadores clientes precisam de um certificado para encriptar com sucesso dados que são desencriptados pela Azure AD DS. Se utilizar um CA público, o computador deve confiar automaticamente nestes emitentes de certificados e ter um certificado correspondente.

Neste tutorial utiliza-se um certificado auto-assinado e gerou um certificado que inclui a chave privada no passo anterior. Agora vamos exportar e, em seguida, instalar o certificado auto-assinado na loja de certificados fidedigna no computador cliente:

1. Volte para o MMC for *Certificates (Computador Local) > loja de Certificados pessoais >.* O certificado auto-assinado criado num passo anterior é apresentado, como *aaddscontoso.com*. Selecione à direita este certificado e, em seguida, escolha **Todas as Tarefas > Exportar...**
1. No **Certificado De Exportação de Assistente**, selecione **Seguinte**.
1. Como não precisa da chave privada para os clientes, na página **'Exportar Chave Privada'** escolha **Não, não exporte a chave privada,** então selecione **Next**.
1. Na página **'Formato de Ficheiros de Exportação',** selecione **Base-64 codificado X.509 (. CER)** como formato de ficheiro para o certificado exportado:

    ![Escolha a opção de exportação do certificado na Base-64 codificada X.509 (. CER) formato de ficheiro](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na página **"Ficheiro para Exportação",** especifique o nome do ficheiro e o local onde pretende exportar o certificado, tais como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Na página de análise, **selecione Finish** para exportar o certificado para um *. Arquivo* de certificado CER. É apresentado um diálogo de confirmação quando o certificado foi exportado com sucesso.

O *. O* ficheiro de certificado CER pode agora ser distribuído aos computadores clientes que precisam confiar na ligação LDAP segura ao domínio gerido. Vamos instalar o certificado no computador local.

1. Abra o File Explorer e navegue no local onde guardou o *. Arquivo* de certificado CER, como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecione à direita *o . Arquivo* de certificado CER, em seguida, escolha **Certificado de Instalação**.
1. No **Certificado De Importação De Feiticeiras,** opte por armazenar o certificado na *máquina Local,* selecione **seguinte**:

    ![Escolha a opção de importar o certificado para a loja de máquinas local](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quando solicitado, escolha **Sim** para permitir que o computador escoda alterações.
1. Opte **por selecionar automaticamente a loja de certificados com base no tipo de certificado** e, em seguida, selecione **Seguinte**.
1. Na página de revisão, **selecione Finish** para importar o *. Certificado CER.* ficheiro Um diálogo de confirmação é apresentado quando o certificado foi importado com sucesso.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Ativar LDAP seguro para Azure AD DS

Com um certificado digital criado e exportado que inclui a chave privada, e o computador cliente definido para confiar na ligação, agora ativar LDAP seguro no seu domínio gerido. Para permitir um LDAP seguro num domínio gerido, execute os seguintes passos de configuração:

1. No [portal Azure,](https://portal.azure.com)introduza *os serviços de domínio* na caixa de recursos de **pesquisa.** Selecione **Azure AD Domain Services** a partir do resultado da pesquisa.
1. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo da janela Azure AD DS, escolha **Secure LDAP**.
1. Por predefinição, o acesso LDAP seguro ao seu domínio gerido é desativado. Toggle **Secure LDAP** para **ativar**.
1. O acesso seguro do LDAP ao seu domínio gerido através da internet é desativado por padrão. Quando ativa o acesso ao LDAP seguro do público, o seu domínio é suscetível a ataques de força bruta de palavra-passe através da internet. No passo seguinte, um grupo de segurança de rede é configurado para bloquear o acesso apenas aos intervalos de endereço IP de origem necessárias.

    Toggle **Deixe o acesso seguro do LDAP através da internet** para **ativar**.

1. Selecione o ícone da pasta ao lado **de . Ficheiro PFX com certificado LDAP seguro**. Navegue pelo caminho do *. Ficheiro PFX,* em seguida, selecione o certificado criado num passo anterior que inclui a chave privada.

    > [!IMPORTANT]
    > Como indicado na secção anterior sobre os requisitos de certificado, não pode utilizar um certificado de um CA público com o domínio *padrão .onmicrosoft.com.* A Microsoft é dona do domínio *.onmicrosoft.com,* por isso um CA público não emitirá um certificado.
    >
    > Certifique-se de que o seu certificado está no formato apropriado. Caso contrário, a plataforma Azure gera erros de validação de certificados quando ativa o LDAP seguro.

1. Introduza a **Palavra-passe para desencriptar . Ficheiro PFX** definido numa etapa anterior quando o certificado foi exportado para *um . Ficheiro PFX.*
1. **Selecione Guardar** para ativar o LDAP seguro.

    ![Ativar lDAP seguro para um domínio gerido no portal Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

É apresentada uma notificação de que o LDAP seguro está a ser configurado para o domínio gerido. Não é possível modificar outras definições para o domínio gerido até que esta operação esteja concluída.

Demora alguns minutos a ativar o LDAP seguro para o seu domínio gerido. Se o certificado LDAP seguro que fornece não corresponder aos critérios exigidos, a ação para permitir a segurança do LDAP para o domínio gerido falha.

Algumas razões comuns para a falha são se o nome de domínio estiver incorreto, o algoritmo de encriptação do certificado não é *TripleDES-SHA1*, ou o certificado expira em breve ou já expirou. Pode recriar o certificado com parâmetros válidos e, em seguida, ativar lDAP seguro utilizando este certificado atualizado.

## <a name="change-an-expiring-certificate"></a>Alterar um certificado de caducidade

1. Crie um certificado LDAP seguro de substituição seguindo as etapas para [criar um certificado para LDAP seguro](#create-a-certificate-for-secure-ldap).
1. Para aplicar o certificado de substituição ao Azure AD DS, no menu esquerdo para Azure AD DS no portal Azure, selecione **Secure LDAP** e, em seguida, selecione **Alterar o Certificado**.
1. Distribua o certificado a todos os clientes que se conectem utilizando LDAP seguro. 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Bloqueie o acesso seguro do LDAP através da internet

Quando ativa o acesso seguro do LDAP através da internet ao seu domínio gerido, cria uma ameaça à segurança. O domínio gerido é acessível a partir da internet na porta TCP 636. Recomenda-se restringir o acesso ao domínio gerido a endereços IP conhecidos específicos para o seu ambiente. Uma regra do grupo de segurança da rede Azure pode ser usada para limitar o acesso a LDAP seguro.

Vamos criar uma regra para permitir o acesso lDAP seguro de entrada sobre a porta TCP 636 a partir de um conjunto especificado de endereços IP especificados. Uma regra *denyAll* padrão com uma prioridade inferior aplica-se a todos os outros tráfegos de entrada a partir da internet, pelo que apenas os endereços especificados podem chegar ao seu domínio gerido usando LDAP seguro.

1. No portal Azure, selecione *grupos de recursos* na navegação do lado esquerdo.
1. Escolha o seu grupo de recursos, como *o myResourceGroup,* selecione o seu grupo de segurança de rede, como *aaads-nsg*.
1. A lista das regras de segurança existentes de entrada e saída são apresentadas. No lado esquerdo das janelas do grupo de segurança da rede, escolha **Definições > regras de segurança de entrada**.
1. **Selecione Adicionar,** em seguida, crie uma regra para permitir a porta *TCP* *636*. Para uma melhor segurança, escolha a fonte como *Endereços IP* e, em seguida, especifique o seu próprio endereço IP válido ou alcance para a sua organização.

    | Definição                           | Valor        |
    |-----------------------------------|--------------|
    | Origem                            | Endereços IP |
    | Endereços IP de origem / gamas CIDR | Um endereço IP válido ou alcance para o seu ambiente |
    | Intervalo de portas de origem                | *            |
    | Destino                       | Qualquer          |
    | Intervalos de portas de destino           | 636          |
    | Protocolo                          | TCP          |
    | Ação                            | Permitir        |
    | Prioridade                          | 401          |
    | Name                              | AllowLDAPS   |

1. Quando estiver pronto, **selecione Adicione** para guardar e aplicar a regra.

    ![Criar uma regra do grupo de segurança de rede para garantir o acesso lDAPS através da internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configurar zona DNS para acesso externo

Com acesso LDAP seguro ativado através da internet, atualize a zona DNS para que os computadores clientes possam encontrar este domínio gerido. O *endereço IP externo Secure LDAP* está listado no **separador Propriedades** para o seu domínio gerido:

![Ver o endereço IP externo LDAP seguro para o seu domínio gerido no portal Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configure o seu fornecedor externo de DNS para criar um registo de anfitrião, como *ldaps,* para resolver este endereço IP externo. Para testar localmente na sua máquina primeiro, pode criar uma entrada no ficheiro anfitriões do Windows. Para editar com sucesso o ficheiro dos anfitriões na sua máquina local, abra o *Notepad* como administrador e, em seguida, abra o ficheiro *C:\Windows\System32\drivers\etc\hosts*

O seguinte exemplo de entrada de DNS, quer com o seu fornecedor externo de DNS, quer no ficheiro de anfitriões locais, resolve o tráfego de *ldaps.aaddscontoso.com* para o endereço IP externo de *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Teste de consultas ao domínio gerido

Para ligar e ligar ao seu domínio gerido e pesquisar sobre o LDAP, utilize a ferramenta *LDP.exe.* Esta ferramenta está incluída no pacote Ferramentas de Administração de Servidor Remoto (RSAT). Para obter mais informações, consulte [instalar Ferramentas de Administração do Servidor Remoto][rsat].

1. Abra *LDP.exe* e ligue-se ao domínio gerido. Selecione **Ligação,** em seguida, escolha **Ligar...**.
1. Introduza o nome de domínio LDAP DNS seguro do seu domínio gerido criado no passo anterior, como *ldaps.aaddscontoso.com*. Para utilizar lDAP seguro, coloque a **Porta** em *636,* em seguida, verifique se a caixa é **SSL**.
1. Selecione **OK** para ligar ao domínio gerido.

Em seguida, ligue-se ao seu domínio gerido. Os utilizadores (e contas de serviço) não podem executar ligações simples LDAP se tiver desativado a sincronização de hash de palavra-passe NTLM no seu domínio gerido. Para obter mais informações sobre a sincronização de hash de palavra-passe NTLM, consulte [Secure your managed domain][secure-domain].

1. Selecione a opção **menu 'Ligação'** e, em seguida, escolha **'Ligar'...**.
1. Forneça as credenciais de uma conta de utilizador que pertence ao domínio gerido. Introduza a palavra-passe da conta de utilizador e, em seguida, introduza o seu domínio, como *aaddscontoso.com*.
1. Para **o tipo Bind**, escolha a opção para Bind com *credenciais*.
1. Selecione **OK** para ligar ao seu domínio gerido.

Para ver os objetos armazenados no seu domínio gerido:

1. Selecione a opção **menu Ver** e, em seguida, escolha **Árvore**.
1. Deixe o campo *BaseDN* em branco e, em seguida, selecione **OK**.
1. Escolha um recipiente, como *Utilizadores AADDC,* em seguida, selecione o recipiente e escolha **Procurar.**
1. Deixe os campos pré-povoados definidos e, em seguida, selecione **Executar**. Os resultados da consulta são apresentados na janela da mão direita, como mostra a seguinte saída de exemplo:

    ![Procure objetos no seu domínio gerido utilizando LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Para consultar diretamente um recipiente específico, a partir do menu **View > Tree,** pode especificar um **BaseDN** como *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* ou *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*. Para obter mais informações sobre como formatar e criar consultas, consulte o [básico da consulta LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Limpar os recursos

Se adicionar uma entrada de DNS ao ficheiro de anfitriões locais do seu computador para testar a conectividade para este tutorial, remova esta entrada e adicione um registo formal na sua zona de DNS. Para remover a entrada do ficheiro dos anfitriões locais, complete os seguintes passos:

1. Na sua máquina local, abra *o Bloco de Notas* como administrador
1. Navegue e abra o ficheiro *C:\Windows\System32\drivers\etc\anfitriões*
1. Apague a linha para o registo que adicionou, tal como `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para uso com Azure AD DS
> * Ativar LDAP seguro para Azure AD DS
> * Configurar LDAP seguro para uso através da internet pública
> * Ligar e testar secure LDAP para um domínio gerido

> [!div class="nextstepaction"]
> [Configurar a sincronização de hash de palavra-passe para um ambiente híbrido Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
