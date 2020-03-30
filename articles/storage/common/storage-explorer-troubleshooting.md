---
title: Guia de resolução de problemas do Azure Storage Explorer Microsoft Docs
description: Visão geral das técnicas de depuração para o Explorador de Armazenamento Azure
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: aec8048c7ef2eb0d944cdd2a863e23578f4f87e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561685"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de resolução de problemas do Explorador de Armazenamento Azure

O Microsoft Azure Storage Explorer é uma aplicação autónoma que facilita o trabalho com dados do Azure Storage no Windows, macOS e Linux. A aplicação pode ligar-se a contas de armazenamento alojadas em Azure, nuvens nacionais e Azure Stack.

Este guia resume soluções para questões que são comumente vistas no Storage Explorer.

## <a name="rbac-permissions-issues"></a>Problemas de permissões RBAC

O controlo de acesso baseado em funções [O RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) permite uma gestão de acesso altamente granular dos recursos Azure, combinando conjuntos de permissões em _funções._ Aqui estão algumas estratégias para que o RBAC funcione da melhor forma no Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Como acedo aos meus recursos no Storage Explorer?

Se você está tendo problemas de acesso a recursos de armazenamento através do RBAC, você pode não ter sido atribuído as funções apropriadas. As seguintes secções descrevem as permissões que o Storage Explorer necessita atualmente para o acesso aos seus recursos de armazenamento. Contacte o administrador da sua conta Azure se não tiver a certeza de que tem as funções ou permissões adequadas.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Ler: Lista/Obter Conta de Armazenamento(s)" emissão de permissões

Deve ter permissão para listar contas de armazenamento. Para obter esta permissão, deve ser-lhe atribuído o papel de _Leitor._

#### <a name="list-storage-account-keys"></a>Lista de chaves da conta de armazenamento

O Storage Explorer também pode usar chaves de conta para autenticar pedidos. Você pode ter acesso a chaves de conta através de papéis mais poderosos, como o papel _de Contribuinte._

> [!NOTE]
> As chaves de acesso concedem permissões sem restrições a quem as detém. Por isso, não recomendamos que entregue estas chaves aos utilizadores da conta. Se precisar revogar as teclas de acesso, pode regenera-las a partir do [portal Azure.](https://portal.azure.com/)

#### <a name="data-roles"></a>Funções de dados

Deve ser-lhe atribuída pelo menos uma função que conceda acesso à leitura de dados a partir de recursos. Por exemplo, se quiser listar ou descarregar bolhas, necessitará, pelo menos, do papel do Leitor de _Dados blob_ de armazenamento.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que preciso de um papel de camada de gestão para ver os meus recursos no Storage Explorer?

O Azure Storage tem duas camadas de acesso: _gestão_ e _dados._ As assinaturas e as contas de armazenamento são acedidas através da camada de gestão. Contentores, bolhas e outros recursos de dados são acedidos através da camada de dados. Por exemplo, se quiser obter uma lista das suas contas de armazenamento do Azure, envie um pedido para o ponto final da gestão. Se quiser uma lista de recipientes blob numa conta, envie um pedido para o ponto final de serviço apropriado.

As funções RBAC podem conter permissões para gestão ou acesso à camada de dados. O papel do Leitor, por exemplo, concede acesso apenas a leitura a recursos de camadas de gestão.

Em rigor, a função Reader não fornece permissões de camada de dados e não é necessária para aceder à camada de dados.

O Storage Explorer facilita o acesso aos seus recursos recolhendo as informações necessárias para se conectar aos seus recursos Azure. Por exemplo, para exibir os seus recipientes de blob, o Storage Explorer envia um pedido de "lista de contentores" para o ponto final do serviço de blob. Para obter esse ponto final, o Storage Explorer procura a lista de subscrições e contas de armazenamento a que tem acesso. Para encontrar as suas subscrições e contas de armazenamento, o Storage Explorer também precisa de acesso à camada de gestão.

Se não tiver um papel que conceda permissões de camada de gestão, o Storage Explorer não consegue obter a informação de que necessita para se ligar à camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não conseguir as permissões da camada de gestão que preciso do meu administrador?

Não temos atualmente uma solução relacionada com o RBAC para esta questão. Como suposições, pode solicitar um SAS URI para [anexar ao seu recurso](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-built-in-rbac-roles"></a>Funções RBAC incorporadas recomendadas

Existem várias funções rBAC incorporadas que podem fornecer as permissões necessárias para usar o Storage Explorer. Alguns desses papéis são:
- [Proprietário](/azure/role-based-access-control/built-in-roles#owner): Gerir tudo, incluindo acesso a recursos. **Nota:** este papel vai dar-lhe acesso chave.
- [Contribuinte](/azure/role-based-access-control/built-in-roles#contributor): Gerir tudo, excluindo o acesso aos recursos. **Nota:** este papel vai dar-lhe acesso chave.
- [Leitor](/azure/role-based-access-control/built-in-roles#reader): Ler e listar recursos.
- [Contribuinte da Conta](/azure/role-based-access-control/built-in-roles#storage-account-contributor)de Armazenamento : Gestão completa das contas de armazenamento. **Nota:** este papel vai dar-lhe acesso chave.
- [Armazenamento Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): Acesso total aos contentores e dados de blob de armazenamento Azure.
- [Armazenamento Blob Data Contributor](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): Leia, escreva e elimine recipientes de armazenamento azure e bolhas.
- [Armazenamento Blob Data Reader](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): Leia e enumere recipientes de armazenamento Azure e bolhas.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado auto-assinado na cadeia de certificados (e erros semelhantes)

Os erros de certificado ocorrem tipicamente numa das seguintes situações:

- A aplicação está conectada através de um _proxy transparente_, o que significa que um servidor (como o seu servidor da empresa) está a intercetar o tráfego HTTPS, desencriptando-o e, em seguida, encriptando-o utilizando um certificado auto-assinado.
- Está a executar uma aplicação que está a injetar um certificado SSL auto-assinado nas mensagens HTTPS que recebe. Exemplos de aplicações que injetam certificados incluem software antivírus e de inspeção de tráfego de rede.

Quando o Storage Explorer vê um certificado auto-assinado ou não confiável, já não sabe se a mensagem HTTPS recebida foi alterada. Se tiver uma cópia do certificado auto-assinado, pode instruir o Explorador de Armazenamento a confiar nele seguindo estes passos:

1. Obtenha uma cópia codificada da Base-64 X.509 (.cer) do certificado.
2. Vá à **Edição** > de**Certificados SSL** > **Import Certificates,** e, em seguida, use o apanhador de ficheiros para encontrar, selecionar e abrir o ficheiro .cer.

Esta emissão também pode ocorrer se houver vários certificados (raiz e intermediário). Para corrigir este erro, ambos os certificados devem ser adicionados.

Se não tem a certeza de onde vem o certificado, siga estes passos para encontrá-lo:

1. Instale openssl.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Qualquer uma das versões de luz deve ser suficiente.
    * Mac e Linux: Deve ser incluído no seu sistema operativo.
2. Run OpenSSL.
    * Janelas: Abra o diretório de instalação, selecione **/bin/** e, em seguida, clique duas **vezes openssl.exe**.
    * Mac e Linux: Corram `openssl` de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`.
4. Procure certificados autoassinados. Se não tem a certeza de quais os certificados auto-assinados, note que em qualquer lugar o sujeito `("s:")` e emissor `("i:")` são os mesmos.
5. Quando encontrar certificados auto-assinados, para cada um, copie `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` cole tudo desde (e incluindo) até um novo ficheiro .cer.
6. Open Storage Explorer e vá para **editar** >  > **certificados**de importação de**certificados SSL**. Em seguida, utilize o apanhador de ficheiros para encontrar, selecionar e abrir os ficheiros .cer que criou.

Se não encontrar certificados auto-assinados seguindo estes passos, contacte-nos através da ferramenta de feedback. Também pode abrir o Storage Explorer a `--ignore-certificate-errors` partir da linha de comando utilizando a bandeira. Quando aberto com esta bandeira, o Storage Explorer ignora erros de certificado.

## <a name="sign-in-issues"></a>Problemas de início de sessão

### <a name="blank-sign-in-dialog-box"></a>Caixa de diálogo de entrada em branco

As caixas de diálogo em branco ocorrem mais frequentemente quando os Serviços da Federação de Diretórios Ativos (AD FS) solicitam ao Explorer de Armazenamento que realize um redirecionamento, que não é suportado por Eletrões. Para resolver este problema, pode tentar utilizar o Device Code Flow para iniciar sessão. Para o fazer, siga estes passos:

1. Na barra de ferramentas vertical esquerda, abra **As definições**. No Painel definições, vá ao **Signo** > de**Inscrição .** Ativar **Utilização Utilização**de código de fluxo de código .
2. Abra a caixa de diálogo **Connect** (através do ícone da ficha na barra vertical do lado esquerdo ou selecionando a **Conta Add** no painel de conta).
3. Escolha o ambiente a que pretende assinar.
4. Selecione **Iniciar sessão**.
5. Siga as instruções no próximo painel.

Se não conseguir iniciar sessão na conta que pretende utilizar porque o seu navegador predefinido já está inscrito numa conta diferente, faça uma das seguintes:

- Copie manualmente o link e o código numa sessão privada do seu navegador.
- Copie manualmente o link e código para um navegador diferente.

### <a name="reauthentication-loop-or-upn-change"></a>Loop de reautenticação ou alteração UPN

Se estiver num ciclo de reautenticação ou tiver alterado a UPN de uma das suas contas, siga estes passos:

1. Remova todas as contas e, em seguida, feche o Storage Explorer.
2. Apagar o . Pasta IdentityService da sua máquina. No Windows, a pasta `C:\users\<username>\AppData\Local`está localizada a . Para O Mac e o Linux, pode encontrar a pasta na raiz do seu diretório de utilizador.
3. Se estiver a executar o Mac ou o Linux, também terá de eliminar a entrada do Microsoft.Developer.IdentityService na loja de chaves do seu sistema operativo. No Mac, a porta-chaves é a aplicação *Gnome Keychain.* Em Linux, a aplicação é tipicamente chamada _de Keyring_, mas o nome pode diferir dependendo da sua distribuição.

### <a name="conditional-access"></a>Acesso Condicional

Devido a uma limitação na Biblioteca AD Azure utilizada pelo Storage Explorer, o Acesso Condicional não é suportado quando o Storage Explorer está a ser utilizado no Windows 10, Linux ou macOS.

## <a name="mac-keychain-errors"></a>Erros mac keychain

O macOS Keychain pode por vezes entrar num estado que causa problemas para a biblioteca de autenticação do Explorador de Armazenamento. Para tirar o Keychain deste estado, siga estes passos:

1. Close Storage Explorer.
2. Abra o porta-chaves (prima Command+Spacebar, digite **o porta-chaves,** e prima Enter).
3. Selecione o porta-chaves "login".
4. Selecione o ícone do cadeado para bloquear o chaveiro. (O cadeado aparecerá bloqueado quando o processo estiver concluído. Pode levar alguns segundos, dependendo das aplicações que tem abertas).

    ![Ícone de cadeado](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Abra o Explorador de Armazenamento.
6. És solicitado com uma mensagem como "O centro de serviço quer aceder ao Keychain." Introduza a sua palavra-passe da conta De administração Mac e selecione **Sempre permitir** (ou **permitir** se **sempre permitir** não estiver disponível).
7. Tente assinar.

### <a name="general-sign-in-troubleshooting-steps"></a>Passos gerais de resolução de problemas

* Se estiver no macOS, e a janela de entrada nunca aparecer sobre a caixa de diálogo de **autenticação,** experimente [estes passos](#mac-keychain-errors).
* Reiniciar o Explorador de Armazenamento.
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Certifique-se de que as definições de procuração e certificado estão corretamente configuradas tanto para a sua máquina como para o Explorador de Armazenamento.
* Se estiver a executar o Windows e tiver acesso ao Visual Studio 2019 na mesma máquina e nas credenciais de inscrição, tente iniciar sessão no Visual Studio 2019. Depois de uma sessão bem sucedida no Visual Studio 2019, pode abrir o Storage Explorer e ver a sua conta no painel de contas.

Se nenhum destes métodos funcionar, [abra um problema no GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Falta de assinaturas e inquilinos avariados

Se não conseguir recuperar as suas subscrições depois de iniciar sessão com sucesso, experimente os seguintes métodos de resolução de problemas:

* Verifique se a sua conta tem acesso às subscrições que espera. Pode verificar o seu acesso inserindo-se no portal para o ambiente Azure que está a tentar utilizar.
* Certifique-se de que assinou através do ambiente azure correto (Azure, Azure China 21Vianet, Azure Germany, Azure Us Government, ou Custom Environment).
* Se estiver atrás de um servidor proxy, certifique-se de ter configurado corretamente o proxy do Storage Explorer.
* Tente remover e readicionar a conta.
* Se houver uma ligação "Mais informação", verifique quais as mensagens de erro que estão a ser reportadas para os inquilinos que estão a falhar. Se não tiver a certeza de como responder às mensagens de erro, sinta-se à vontade para [abrir um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Não pode remover uma conta anexa ou recurso de armazenamento

Se não conseguir remover uma conta ou recurso de armazenamento anexado através da UI, pode eliminar manualmente todos os recursos anexados eliminando as seguintes pastas:

* Janelas:`%AppData%/StorageExplorer`
* macOS:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> Feche o Explorer antes de eliminar estas pastas.

> [!NOTE]
> Se já tiver importado quaisquer certificados SSL, `certs` faça o seu conteúdo do diretório. Mais tarde, pode utilizar o backup para reimportar os seus certificados SSL.

## <a name="proxy-issues"></a>Questões de procuração

Em primeiro lugar, certifique-se de que as seguintes informações que inseriu estão corretas:

* O URL proxy e o número da porta
* Nome de utilizador e senha se o representante os exigir

> [!NOTE]
> O Storage Explorer não suporta ficheiros proxy auto-config para configurar as definições de procuração.

### <a name="common-solutions"></a>Soluções comuns

Se ainda estiver com problemas, experimente os seguintes métodos de resolução de problemas:

* Se conseguir ligar-se à internet sem utilizar o seu proxy, verifique se o Storage Explorer funciona sem configurações de procuração ativadas. Se for esse o caso, pode haver um problema com as definições de procuração. Trabalhe com o seu administrador para identificar os problemas.
* Verifique se outras aplicações que utilizam o servidor proxy funcionam como esperado.
* Verifique se pode ligar-se ao portal para o ambiente Azure que está a tentar utilizar.
* Verifique se pode receber respostas dos seus pontos finais de serviço. Introduza um dos seus URLs de ponto final no seu navegador. Se conseguir ligar-se, deve receber InvalidQueryParameterValue ou uma resposta XML semelhante.
* Se alguém estiver também a usar o Storage Explorer com o seu servidor proxy, verifique se pode ligar-se. Se puderem, poderá ter de contactar o administrador do seu servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se tiver ferramentas de networking, como o Fiddler para Windows, pode diagnosticar os problemas da seguinte forma:

* Se tiver de trabalhar através do seu representante, poderá ter de configurar a sua ferramenta de rede para se ligar através do proxy.
* Verifique o número da porta utilizado pela sua ferramenta de rede.
* Introduza o URL do hospedeiro local e o número de porta da ferramenta de rede como configurações de procuração no Storage Explorer. Quando o fizer corretamente, a sua ferramenta de networking inicia a sessão de pedidos de rede feitos pelo Storage Explorer para pontos finais de gestão e serviço. Por exemplo, `https://cawablobgrs.blob.core.windows.net/` introduza o seu ponto final blob num browser, e receberá uma resposta que se assemelha ao seguinte:

  ![Exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Esta resposta sugere que o recurso existe, mesmo que não consiga aceder-lhe.

### <a name="contact-proxy-server-admin"></a>Contacte a administração do servidor proxy

Se as definições de procuração estiverem corretas, poderá ter de contactar o administrador do seu servidor proxy para:

* Certifique-se de que o seu representante não bloqueia o tráfego para a gestão do Azure ou pontos finais de recursos.
* Verifique o protocolo de autenticação utilizado pelo seu servidor proxy. O Storage Explorer não suporta atualmente os proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Incapaz de recuperar crianças"

Se estiver ligado ao Azure através de um proxy, verifique se as definições de procuração estão corretas. Se lhe for concedido acesso a um recurso do proprietário da subscrição ou conta, verifique se leu ou lista as permissões para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A cadeia de ligação não tem definições de configuração completas

Se receber esta mensagem de erro, é possível que não tenha as permissões necessárias para obter as chaves da sua conta de armazenamento. Para confirmar que é esse o caso, vá ao portal e localize a sua conta de armazenamento. Pode fazê-lo clicando no nó direito da sua conta de armazenamento e selecionando **Open in Portal**. Depois, vai para a lâmina das Chaves de **Acesso.** Se não tiver permissão para ver as chaves, verá uma mensagem de "Não tem acesso". Para contornar este problema, pode obter a chave da conta de outra pessoa e anexar através do nome e da chave, ou pode pedir a alguém um SAS na conta de armazenamento e usá-la para anexar a conta de armazenamento.

Se vir as chaves da conta, apresente um problema no GitHub para que possamos ajudá-lo a resolver o problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Erro ocorreu ao adicionar nova ligação: TypeError: Não pode ler a 'versão' da propriedade de indefinido

Se receber esta mensagem de erro quando tentar adicionar uma ligação personalizada, os dados de ligação armazenados no gestor de credenciais locais podem ser corrompidos. Para resolver este problema, tente apagar as suas ligações locais corrompidas e, em seguida, readicione-as:

1. Inicie o Storage Explorer. A partir do menu, vá para ajudar a > **alternar ferramentas**de desenvolvimento . **Help**
2. Na janela aberta, no separador **Aplicação,** vá ao **Armazenamento Local** (lado esquerdo) > **file://**.
3. Dependendo do tipo de ligação com que está a ter um problema, procure a sua chave e, em seguida, copie o seu valor para um editor de texto. O valor é uma variedade dos seus nomes de ligação personalizados, como os seguintes:
    * Contas de armazenamento
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Recipientes blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Partilhas de Ficheiros
        * `StorageExplorer_CustomConnections_Files_v1`
    * Filas
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelas
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Depois de guardar os nomes de ligação `[]`atuais, detete o valor em Ferramentas de Desenvolvimento para .

Se quiser preservar as ligações que não estão corrompidas, pode usar os seguintes passos para localizar as ligações corrompidas. Se não se importar de perder todas as ligações existentes, pode saltar estes passos e seguir as instruções específicas da plataforma para limpar os dados de ligação.

1. A partir de um editor de texto, volte a adicionar cada nome de ligação às Ferramentas de Desenvolvimento e, em seguida, verifique se a ligação ainda está a funcionar.
2. Se uma ligação estiver a funcionar corretamente, não está corrompida e podedeixá-la com segurança. Se uma ligação não estiver a funcionar, retire o seu valor das Ferramentas de Desenvolvimento e grave-a para que possa adicioná-la mais tarde.
3. Repita até examinar todas as suas ligações.

Depois de analisar todas as suas ligações, para todas as ligações nomes que não são adicionados de volta, você deve limpar os seus dados corrompidos (se houver algum) e adicioná-los de volta usando os passos padrão no Explorador de Armazenamento:

# <a name="windows"></a>[Windows](#tab/Windows)

1. No menu **Iniciar,** procure o **Credential Manager** e abra-o.
2. Vá para **as credenciais do Windows.**
3. Sob **credenciais genéricas,** procure `<connection_type_key>/<corrupted_connection_name>` entradas que `StorageExplorer_CustomConnections_Accounts_v1/account1`tenham a chave (por exemplo, ).
4. Apague estas entradas e readicione as ligações.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Holofotes Abertos (Barra de Comando+Spacebar) e procurem acesso ao **Keychain**.
2. Procure entradas que `<connection_type_key>/<corrupted_connection_name>` tenham a chave `StorageExplorer_CustomConnections_Accounts_v1/account1`(por exemplo, ).
3. Apague estas entradas e readicione as ligações.

# <a name="linux"></a>[Linux](#tab/Linux)

A gestão da credencial local varia consoante a distribuição do Linux. Se a sua distribuição Linux não fornecer uma ferramenta GUI incorporada para gestão de credenciais locais, pode instalar uma ferramenta de terceiros para gerir as suas credenciais locais. Por exemplo, você pode usar [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), uma ferramenta GUI de código aberto para gerir credenciais locais Linux.

1. Abra a sua ferramenta local de gestão de credenciais e encontre as suas credenciais guardadas.
2. Procure entradas que `<connection_type_key>/<corrupted_connection_name>` tenham a chave `StorageExplorer_CustomConnections_Accounts_v1/account1`(por exemplo, ).
3. Apague estas entradas e readicione as ligações.
---

Se ainda encontrar este erro depois de executar estes passos, ou se quiser partilhar o que suspeita ter corrompido as ligações, [abra um problema](https://github.com/microsoft/AzureStorageExplorer/issues) na nossa página do GitHub.

## <a name="issues-with-sas-url"></a>Problemas com URL SAS

Se estiver a ligar-se a um serviço através de um URL SAS e a experimentar um erro:

* Verifique se o URL fornece as permissões necessárias para ler ou listar recursos.
* Verifique se o URL não expirou.
* Se o URL SAS se basear numa política de acesso, verifique se a política de acesso não foi revogada.

Se tiver acidentalmente ligado utilizando um URL SAS inválido e agora não conseguir separar-se, siga estes passos:

1. Quando estiver a executar o Storage Explorer, prima F12 para abrir a janela Ferramentas de Desenvolvimento.
2. No separador **Aplicação,** selecione **File:// de Armazenamento** > Local**na** árvore à esquerda.
3. Encontre a chave associada ao tipo de serviço do problemático SAS URI. Por exemplo, se o mau SAS URI for para um `StorageExplorer_AddStorageServiceSAS_v1_blob`recipiente de bolhas, procure a chave chamada .
4. O valor da chave deve ser uma matriz JSON. Encontre o objeto associado ao URI mau e, em seguida, apague-o.
5. Prima Ctrl+R para recarregar o Explorador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências linux

Storage Explorer 1.10.0 e mais tarde está disponível como um instantâneo da Snap Store. O snap Do Storage Explorer instala automaticamente todas as suas dependências e é atualizado quando uma nova versão do instantâneo está disponível. Instalar o encaixe do Storage Explorer é o método de instalação recomendado.

O Storage Explorer requer a utilização de um gestor de passwords, que poderá ser necessário ligar manualmente antes de o Storage Explorer funcionar corretamente. Pode ligar o Storage Explorer ao gestor de passwords do seu sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Também pode descarregar a aplicação como um ficheiro .tar.gz, mas terá de instalar dependências manualmente.

> [!IMPORTANT]
> O Storage Explorer, conforme fornecido no download .tar.gz, é suportado apenas para distribuição ubuntu. Outras distribuições não foram verificadas e podem exigir pacotes alternativos ou adicionais.

Estes pacotes são os requisitos mais comuns para o Storage Explorer em Linux:

* [.NET Core 2.2 Tempo de execução](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` ou `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer versão 1.7.0 e anteriormente requerem .NET Core 2.0. Se tiver uma versão mais recente do .NET Core instalada, terá de [remendar](#patching-storage-explorer-for-newer-versions-of-net-core)o Storage Explorer . Se estiver a executar o Storage Explorer 1.8.0 ou mais tarde, deverá poder utilizar até .NET Core 2.2. As versões para além de 2.2 não foram verificadas para funcionar neste momento.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Baixe o Storage Explorer.
2. Instale o prazo [de execução do núcleo .NET](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Baixe o Storage Explorer.
2. Instale o prazo [de execução do núcleo .NET](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Baixe o Storage Explorer.
2. Instale o prazo [de execução do núcleo .NET](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Baixe o Storage Explorer.
2. Instale o prazo [de execução do núcleo .NET](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Execute o seguinte comando:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer para versões mais recentes de .NET Core

Para o Storage Explorer 1.7.0 ou mais cedo, poderá ter de remendar a versão do Núcleo .NET utilizado pelo Storage Explorer:

1. Descarregue a versão 1.5.43 do StreamJsonRpc [a partir de NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "Pacote de descarregamento" no lado direito da página.
2. Depois de descarregar o pacote, `.nupkg` altere a extensão do ficheiro para `.zip`.
3. Desaperte o pacote.
4. Abra `streamjsonrpc.1.5.43/lib/netstandard1.1/` a pasta.
5. Copiar `StreamJsonRpc.dll` para os seguintes locais na pasta Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Open In Explorer" do portal Azure não funciona

Se o botão **Open In Explorer** no portal Azure não funcionar, certifique-se de que está a utilizar um navegador compatível. Os seguintes navegadores foram testados para compatibilidade:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Passos seguintes

Se nenhuma destas soluções funcionar para si, [abra um problema no GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues) Também pode fazê-lo selecionando o problema do Relatório para o botão **GitHub** no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)
