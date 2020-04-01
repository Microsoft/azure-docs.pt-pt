---
title: Tutorial - Configure LDAPS para Serviços de Domínio de Diretório Ativo Azure [ Microsoft Docs
description: Neste tutorial, você aprende como configurar o protocolo de acesso seguro ao diretório leve (LDAPS) para um domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 636f2e6139ad081d1e2fc67462a74cb7e18e3ff0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475836"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Configure o LDAP seguro para um domínio gerido pelo Azure Ative Directory Domain Services

Para comunicar com o seu domínio de domínio ativo Azure (Azure AD DS) gerido, é utilizado o Protocolo de Acesso ao Diretório Leve (LDAP). Por padrão, o tráfego LDAP não está encriptado, o que é uma preocupação de segurança para muitos ambientes. Com o Azure AD DS, pode configurar o domínio gerido para utilizar o Protocolo de Acesso ao Diretório Leve seguro (LDAPS). Quando utiliza LDAP seguro, o tráfego é encriptado. O LDAP seguro também é conhecido como LDAP sobre a camada de tomadas seguras (SSL) / Segurança da camada de transporte (TLS).

Este tutorial mostra-lhe como configurar o LDAPS para um domínio gerido por DS Azure AD.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para utilização com AD DS Azure
> * Ativar LDAP seguro para DS AD Azure
> * Configure lDAP seguro para uso através da internet pública
> * Ligar e testar lDAP seguro para um domínio gerido por DS Azure AD

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].
* A ferramenta *LDP.exe* instalada no seu computador.
    * Se necessário, instale as Ferramentas de Administração do [Servidor Remoto (RSAT)][rsat] para serviços de domínio de *diretório ativo e LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Neste tutorial, configura o lDAP seguro para o domínio gerido pelo Azure AD DS utilizando o portal Azure. Para começar, inicie a inscrição no [portal Azure.](https://portal.azure.com)

## <a name="create-a-certificate-for-secure-ldap"></a>Criar um certificado para LDAP seguro

Para utilizar LDAP seguro, um certificado digital é usado para encriptar a comunicação. Este certificado digital é aplicado ao seu domínio gerido pelo Azure AD DS e permite que ferramentas como *lDP.exe* utilizem comunicação encriptada segura ao consultar dados. Existem duas formas de criar um certificado para acesso lDAP seguro ao domínio gerido:

* Um certificado de uma autoridade de certificados públicos (CA) ou de uma empresa CA.
    * Se a sua organização receber certificados de uma AC pública, obtenha o certificado LDAP seguro desse CA público. Se utilizar um CA empresarial na sua organização, obtenha o certificado LDAP seguro da empresa CA.
    * Um CA público só funciona quando se utiliza um nome DNS personalizado com o seu domínio gerido azure AD DS. Se o nome de domínio DNS do seu domínio gerido terminar em *.onmicrosoft.com,* não pode criar um certificado digital para garantir a ligação com este domínio predefinido. A Microsoft é dona do domínio *.onmicrosoft.com,* para que um CA público não emita um certificado. Neste cenário, crie um certificado auto-assinado e use-o para configurar o LDAP seguro.
* Um certificado auto-assinado que crias a ti mesmo.
    * Esta abordagem é boa para fins de teste, e é o que este tutorial mostra.

O certificado que solicita ou cria deve satisfazer os seguintes requisitos. O seu domínio gerido encontra problemas se ativar o LDAP seguro com um certificado inválido:

* **Emitente fidedigno** - O certificado deve ser emitido por uma autoridade fidedigna por computadores que se ligam ao domínio gerido utilizando LDAP seguro. Esta autoridade pode ser uma CA pública ou uma Enterprise CA confiada por estes computadores.
* **Vida útil** - O certificado deve ser válido pelo menos durante os próximos 3 a 6 meses. O acesso lDAP seguro ao seu domínio gerido é interrompido quando o certificado expirar.
* **Nome** do assunto - O nome do assunto no certificado deve ser o seu domínio gerido. Por exemplo, se o seu domínio for nomeado *aaddscontoso.com,* o nome do certificado deve ser **.aaddscontoso.com*.
    * O nome DNS ou o nome alternativo do certificado devem ser um certificado wildcard para garantir que o LDAP seguro funcione corretamente com os Serviços de Domínio Azure AD. Os Controladores de Domínio utilizam nomes aleatórios e podem ser removidos ou adicionados para garantir que o serviço permanece disponível.
* **Utilização da chave** - O certificado deve ser configurado para *assinaturas digitais* e chave de *precipherherment*.
* **Final idade** do certificado - O certificado deve ser válido para autenticação do servidor TLS.

Existem várias ferramentas disponíveis para criar certificado auto-assinado, tais como OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet etc. Neste tutorial, vamos criar um certificado auto-assinado para LDAP seguro usando o cmdlet [New-SelfSignedCertificate.][New-SelfSignedCertificate] Abra uma janela PowerShell como **Administrador** e execute os seguintes comandos. Substitua a variável *$dnsName* pelo nome DNS utilizado pelo seu próprio domínio gerido, como *aaddscontoso.com:*

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A saída de exemplo seguinte mostra que o certificado foi gerado com sucesso e está armazenado na loja de certificados local *(LocalMachine\MY):*

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

Para utilizar LDAP seguro, o tráfego de rede é encriptado utilizando infraestruturas de chaves públicas (PKI).

* Uma chave **privada** é aplicada ao domínio gerido pela AD DS azure.
    * Esta chave privada é usada para *desencriptar* o tráfego lDAP seguro. A chave privada só deve ser aplicada ao domínio gerido pelo Azure AD DS e não amplamente distribuída aos computadores clientes.
    * Um certificado que inclui a chave privada utiliza o *. Formato de ficheiro PFX.*
* Aplica-se uma chave **pública** aos computadores dos clientes.
    * Esta chave pública é usada para *encriptar* o tráfego LDAP seguro. A chave pública pode ser distribuída para computadores clientes.
    * Os certificados sem a chave privada utilizam o *. Formato de* ficheiro CER.

Estas duas chaves, as chaves *privadas* e *públicas,* certifiquem-se de que apenas os computadores apropriados podem comunicar com sucesso entre si. Se utilizar um CA público ou uma CA empresarial, é emitido com um certificado que inclui a chave privada e pode ser aplicado a um domínio gerido por AD DS azure. A chave pública já deve ser conhecida e fidedigna por computadores clientes. Neste tutorial, criou um certificado auto-assinado com a chave privada, pelo que necessita de exportar os componentes privados e públicos apropriados.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportar um certificado para a AD DS Azure

Antes de poder utilizar o certificado digital criado no passo anterior com o seu domínio gerido pela Azure AD DS, exporte o certificado para *a . Ficheiro de* certificado PFX que inclui a chave privada.

1. Para abrir o diálogo *Executar,* selecione as teclas **Windows** + **R.**
1. Abra a Consola de Gestão da Microsoft (MMC) inserindo **mmc** no diálogo *Executar* e, em seguida, selecione **OK**.
1. No pedido de Controlo de **Conta do Utilizador,** então selecione **Sim** para lançar MMC como administrador.
1. A partir do menu **'Ficheiro',** selecione **Adicionar/Remover snap-in...**
1. No assistente de encaixe dos **Certificados,** escolha a **conta de Computador**e, em seguida, selecione **Next**.
1. Na página **Select Computer,** escolha **computador local: (o computador em**que esta consola está a funcionar) e, em seguida, selecione **Terminar**.
1. No diálogo **Add or Remove Snap-ins,** selecione **OK** para adicionar os certificados snap-in ao MMC.
1. Na janela MMC, expanda a **Raiz da Consola.** Selecione **Certificados (Computador Local)** e, em seguida, expanda o nó **pessoal,** seguido do nó de **Certificados.**

    ![Abra a loja de certificados pessoais na Consola de Gestão da Microsoft](./media/tutorial-configure-ldaps/open-personal-store.png)

1. O certificado auto-assinado criado no escalão anterior é apresentado, *como*aaddscontoso.com . Selecione este certificado e escolha **todas as tarefas > exportação...**

    ![Certificado de exportação na Consola de Gestão da Microsoft](./media/tutorial-configure-ldaps/export-cert.png)

1. No **Certificado De Saque de Exportação,** selecione **Seguinte**.
1. A chave privada do certificado deve ser exportada. Se a chave privada não estiver incluída no certificado exportado, a ação para permitir o acesso seguro ao LDAP para o seu domínio gerido falha.

    Na página **Chave Privada de Exportação,** escolha **Sim, exporte a chave privada,** em seguida, selecione **Next**.
1. Os domínios geridos pela Azure AD DS apenas suportam o *. Formato de* ficheiro de certificado PFX que inclui a chave privada. Não exporte o certificado *como. Formato* de ficheiro de certificado CER sem a chave privada.

    Na página formato de formato de **ficheiros de exportação,** selecione **Personal Information Exchange - PKCS #12 (. PFX)** como formato de ficheiro para o certificado exportado. Verifique a caixa incluir *todos os certificados na via de certificação, se possível:*

    ![Escolha a opção de exportar o certificado no PKCS 12 (. Formato de ficheiro PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Como este certificado é usado para desencriptar dados, deve controlar cuidadosamente o acesso. Pode ser utilizada uma palavra-passe para proteger a utilização do certificado. Sem a senha correta, o certificado não pode ser aplicado a um serviço.

    Na página **de Segurança,** escolha a opção de **Palavra-passe** para proteger o *. Ficheiro de certificado PFX.* Introduza e confirme uma palavra-passe e, em seguida, selecione **Next**. Esta palavra-passe é utilizada na secção seguinte para ativar o LDAP seguro para o seu domínio gerido pelo Azure AD DS.
1. Na página **De Registo para Exportação,** especifique o nome do ficheiro e a localização onde pretende exportar o certificado, como *C:\Users\accountname\azure-ad-ds.pfx*. Mantenha uma nota da senha e localização do *. Ficheiro PFX,* uma vez que esta informação seria necessária nos próximos passos.
1. Na página de revisão, selecione **Terminar** para exportar o certificado para *a . Ficheiro de certificado PFX.* É apresentado um diálogo de confirmação quando o certificado tiver sido exportado com sucesso.
1. Deixe o MMC aberto para utilização na seguinte secção.

### <a name="export-a-certificate-for-client-computers"></a>Exportar um certificado para computadores clientes

Os computadores clientes devem confiar no emitente do certificado LDAP seguro para poder ligar com sucesso ao domínio gerido utilizando o LDAPS. Os computadores clientes precisam de um certificado para encriptar com sucesso dados que são desencriptados pelo Azure AD DS. Se utilizar um CA público, o computador deve confiar automaticamente nestes emitentes de certificados e ter um certificado correspondente. Neste tutorial você usa um certificado auto-assinado, e gerou um certificado que inclui a chave privada no passo anterior. Agora vamos exportar e, em seguida, instalar o certificado auto-assinado na loja de certificados fidedigna no computador cliente:

1. Volte ao MMC para Certificados (Computador Local) > loja de *Certificados de > Pessoais.* O certificado auto-assinado criado num passo anterior é mostrado, como *aaddscontoso.com*. Selecione este certificado e escolha **todas as tarefas > exportação...**
1. No **Certificado De Saque de Exportação,** selecione **Seguinte**.
1. Como não precisa da chave privada para os clientes, na página **Chave Privada de Exportação** escolha **Não, não exporte a chave privada,** em seguida, selecione **Next**.
1. Na página formato de formato de ficheiro de **exportação,** selecione **Base-64 codificada X.509 (. CER)** como formato de ficheiro para o certificado exportado:

    ![Escolha a opção de exportar o certificado na Base-64 codificada X.509 (. Formato de ficheiro CER)](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na página **De Registo para Exportação,** especifique o nome do ficheiro e a localização onde pretende exportar o certificado, como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Na página de revisão, selecione **Terminar** para exportar o certificado para *a . Arquivo* de certificado cer. É apresentado um diálogo de confirmação quando o certificado tiver sido exportado com sucesso.

*O. *O ficheiro de certificado CER pode agora ser distribuído para computadores clientes que precisam confiar na ligação LDAP segura ao domínio gerido pelo Azure AD DS. Vamos instalar o certificado no computador local.

1. Abra o Explorador de Ficheiros e navegue até ao local onde guardou o *. Ficheiro* de certificado CER, tal como *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecione à direita o *. Arquivo* de certificado CER, em seguida, escolha **Certificado de Instalação**.
1. No **Certificado De Import Wizard,** escolha guardar o certificado na *máquina Local*e, em seguida, selecione **Seguinte:**

    ![Escolha a opção de importar o certificado para a loja de máquinas local](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Quando solicitado, escolha **Sim** para permitir que o computador faça alterações.
1. Escolha **selecionar automaticamente a loja de certificados com base no tipo de certificado**e, em seguida, selecione **Next**.
1. Na página de revisão, selecione **Terminar** para importar o *. Certificado de cer.* ficheiro Um diálogo de confirmação é apresentado quando o certificado tiver sido importado com sucesso.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Ativar LDAP seguro para DS AD Azure

Com um certificado digital criado e exportado que inclui a chave privada, e o computador cliente definido para confiar na ligação, agora permite lDAP seguro no seu domínio gerido azure AD DS. Para permitir o LDAP seguro num domínio gerido por AD DS Azure, execute os seguintes passos de configuração:

1. No [portal Azure,](https://portal.azure.com)insira *serviços* de domínio na caixa **de recursos de Pesquisa.** Selecione Serviços de **Domínio Azure AD** a partir do resultado da pesquisa.
1. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo da janela Azure AD DS, escolha **Secure LDAP**.
1. Por predefinição, o acesso lDAP seguro ao seu domínio gerido é desativado. Alternar **o LDAP seguro** para **ativar**.
1. O acesso LDAP seguro ao seu domínio gerido através da internet é desativado por padrão. Ao ativar o acesso lDAP seguro ao público, o seu domínio é suscetível a ataques de força bruta de senha através da internet. No passo seguinte, um grupo de segurança de rede está configurado para bloquear o acesso apenas às gamas de endereços IP de origem necessárias.

    Toggle **Permitir o acesso lDAP seguro através da internet** para **ativar**.

1. Selecione o ícone da pasta ao lado de **. Ficheiro PFX com certificado LDAP seguro**. Navegue pelo caminho do *. Ficheiro PFX* e, em seguida, selecione o certificado criado num passo anterior que inclui a chave privada.

    Como notado na secção anterior sobre os requisitos de certificado, não pode utilizar um certificado de uma CA pública com o domínio *padrão .onmicrosoft.com.* A Microsoft é dona do domínio *.onmicrosoft.com,* para que um CA público não emita um certificado. Certifique-se de que o seu certificado está no formato apropriado. Caso contrário, a plataforma Azure gera erros de validação de certificados quando ativa o LDAP seguro.

1. Introduza a **Palavra-passe para desencriptar . Ficheiro PFX** definido num passo anterior quando o certificado foi exportado para *a . Ficheiro PFX.*
1. Selecione **Guardar** para ativar o LDAP seguro.

    ![Ativar o LDAP seguro para um domínio gerido azure AD DS no portal Azure](./media/tutorial-configure-ldaps/enable-ldaps.png)

É apresentada uma notificação que o LDAP seguro está a ser configurado para o domínio gerido. Não pode modificar outras definições para o domínio gerido até que esta operação esteja concluída.

Leva alguns minutos para ativar o LDAP seguro para o seu domínio gerido. Se o certificado LDAP seguro que fornece não corresponder aos critérios exigidos, a ação para permitir o LDAP seguro para o domínio gerido falha. Algumas razões comuns para a falha são se o nome de domínio estiver incorreto, ou se o certificado expirar em breve ou já expirar. Pode recriar o certificado com parâmetros válidos e, em seguida, ativar o LDAP seguro utilizando este certificado atualizado.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Bloqueie o acesso lDAP seguro através da internet

Ao ativar o acesso lDAP seguro através da internet ao seu domínio gerido pelo Azure AD DS, cria uma ameaça à segurança. O domínio gerido é acessível a partir da internet na porta TCP 636. É aconselhável restringir o acesso ao domínio gerido a endereços IP conhecidos específicos para o seu ambiente. Uma regra do grupo de segurança da rede Azure pode ser usada para limitar o acesso a LDAP seguro.

Vamos criar uma regra que permita o acesso lDAP seguro de entrada sobre a porta TCP 636 a partir de um conjunto especificado de endereços IP. Uma regra *padrão DenyAll* com uma prioridade mais baixa aplica-se a todos os outros tráfegos de entrada a partir da internet, pelo que apenas os endereços especificados podem chegar ao seu domínio gerido pelo Azure AD DS utilizando LDAP seguro.

1. No portal Azure, selecione *Grupos de Recursos* na navegação lateral esquerda.
1. Escolha o seu grupo de recursos, como o *myResourceGroup,* e selecione o seu grupo de segurança de rede, como *aaads-nsg*.
1. A lista das regras de segurança de entrada e saída existentes é apresentada. No lado esquerdo das janelas do grupo de segurança da rede, escolha **Definições > regras**de segurança de entrada .
1. Selecione **Adicionar**e, em seguida, criar uma regra para permitir a porta *TCP* *636*. Para uma maior segurança, escolha a fonte como *Endereços IP* e, em seguida, especifique o seu próprio endereço IP válido ou intervalo para a sua organização.

    | Definição                           | Valor        |
    |-----------------------------------|--------------|
    | Origem                            | Endereços IP |
    | Endereços IP de origem / gamas CIDR | Um endereço IP válido ou intervalo para o seu ambiente |
    | Intervalo de portas de origem                | *            |
    | Destino                       | Qualquer          |
    | Intervalos de portas de destino           | 636          |
    | Protocolo                          | TCP          |
    | Ação                            | Permitir        |
    | Prioridade                          | 401          |
    | Nome                              | AllowLDAPS   |

1. Quando estiver pronto, selecione **Adicionar** para guardar e aplicar a regra.

    ![Criar uma regra do grupo de segurança da rede para garantir o acesso lDAPS através da internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Configure a zona DNS para acesso externo

Com acesso LDAP seguro ativado através da internet, atualize a zona DNS para que os computadores clientes possam encontrar este domínio gerido. O *endereço IP externo Secure LDAP* está listado no separador **Propriedades** para o seu domínio gerido pelo Azure AD DS:

![Veja o endereço IP externo lDAP seguro para o seu domínio gerido azure AD DS no portal Azure](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configure o seu fornecedor externo de DNS para criar um registo de hospedeiro, como *o Ldaps,* para resolver este endereço IP externo. Para testar localmente a sua máquina primeiro, pode criar uma entrada no ficheiro de anfitriões do Windows. Para editar com sucesso o ficheiro dos anfitriões na sua máquina local, abra o Bloco de *Notas* como administrador e abra o ficheiro *C:\Windows\System32\drivers\etc*

O seguinte exemplo de entrada dNS, quer com o seu fornecedor externo de DNS quer no ficheiro de anfitriões locais, resolve o tráfego para *ldaps.aaddscontoso.com* para o endereço IP externo de *168.62.205.103:*

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Teste de consultas ao domínio gerido

Para ligar e ligar-se ao seu domínio gerido pelo Azure AD DS e pesquisar sobre o LDAP, utilize a ferramenta *LDP.exe.* Esta ferramenta está incluída no pacote Remote Server Administration Tools (RSAT). Para mais informações, consulte instalar ferramentas de administração do [servidor remoto.][rsat]

1. Abra *o LDP.exe* e ligue-se ao domínio gerido. Selecione **Ligação**e, em seguida, escolha **Ligar...**.
1. Introduza o nome de domínio DNS LDAP seguro do seu domínio gerido criado no passo anterior, como *ldaps.aaddscontoso.com*. Para utilizar LDAP seguro, coloque a **Porta** para *636*e, em seguida, verifique a caixa para **SSL**.
1. Selecione **OK** para ligar ao domínio gerido.

Em seguida, ligue-se ao seu domínio gerido azure AD DS. Os utilizadores (e contas de serviço) não podem executar ligações simples LDAP se tiver desativado a sincronização de hash de senha NTLM na sua instância Azure AD DS. Para obter mais informações sobre a sincronização de hash de senha NTLM incapacitante, consulte [Secure your Azure AD DS managed domain][secure-domain].

1. Selecione a opção de menu **Ligação** e, em seguida, escolha **Bind...**.
1. Fornecer as credenciais de uma conta de utilizador pertencente ao grupo de administradores da *AAD DC,* como *a contosoadmina.* Introduza a palavra-passe da conta de utilizador e introduza o seu domínio, como *aaddscontoso.com*.
1. Para o **tipo Bind,** escolha a opção para *Ligar com credenciais*.
1. Selecione **OK** para ligar ao seu domínio gerido azure AD DS.

Para ver os objetos armazenados no seu domínio gerido pela AD DS:

1. Selecione a opção de menu **Ver** e, em seguida, escolha **Tree**.
1. Deixe o campo *BaseDN* em branco e, em seguida, selecione **OK**.
1. Escolha um recipiente, como *utilizadores AADDC,* então selecione o recipiente à direita e escolha **Procurar**.
1. Deixe os campos pré-povoados definidos e, em seguida, selecione **Executar**. Os resultados da consulta são apresentados na janela da direita, como mostra a seguinte saída exemplo:

    ![Procure objetos no seu domínio gerido azure AD DS usando LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Para consultar diretamente um recipiente específico, a partir do menu **View > Tree,** pode especificar um **BaseDN** como *utilizadores OU=AADDC,DC=AADDSCONTOSO,DC=COM* ou *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*. Para obter mais informações sobre como formatar e criar consultas, consulte os [fundamentos básicos da consulta LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver adicionado uma entrada DNS ao ficheiro de anfitriões locais do seu computador para testar a conectividade deste tutorial, remova esta entrada e adicione um registo formal na sua zona DNS. Para remover a entrada do ficheiro de anfitriões locais, complete os seguintes passos:

1. Na sua máquina local, abra *o Bloco* de Notas como administrador
1. Navegue e abra o ficheiro *C:\Windows\System32\drivers\etc*
1. Apagar a linha para o registo que adicionou, como`168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um certificado digital para utilização com AD DS Azure
> * Ativar LDAP seguro para DS AD Azure
> * Configure lDAP seguro para uso através da internet pública
> * Ligar e testar lDAP seguro para um domínio gerido por DS Azure AD

> [!div class="nextstepaction"]
> [Configure sincronização de hash de senha para um ambiente híbrido Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
