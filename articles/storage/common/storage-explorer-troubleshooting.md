---
title: Guia de resolução de problemas do Azure Storage Explorer | Microsoft Docs
description: Visão geral das técnicas de depuração para o Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568544"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de resolução de problemas do Explorador de Armazenamento do Azure

O Explorador de Armazenamento do Microsoft Azure é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento do Microsoft Azure no Windows, macOS e Linux. A aplicação pode ligar-se às contas de armazenamento alojadas no Azure, nas clouds nacionais e no Azure Stack.

Este guia resume soluções para problemas que são geralmente vistos no Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC permissões questões

O controlo de acesso baseado em funções [Azure RBAC](../../role-based-access-control/overview.md) permite uma gestão de acesso altamente granular dos recursos Azure, combinando conjuntos de permissões em _funções._ Aqui estão algumas estratégias para fazer com que o Azure RBAC funcione da melhor forma no Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Como acesso os meus recursos no Storage Explorer?

Se tiver problemas em aceder aos recursos de armazenamento através do Azure RBAC, pode não lhe ter sido atribuído o papel apropriado. As seguintes secções descrevem as permissões que o Storage Explorer atualmente necessita para aceder aos seus recursos de armazenamento. Contacte o seu administrador de conta Azure se não tiver a certeza de que tem as funções ou permissões adequadas.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Ler: Lista/Obter Conta de Armazenamento(s)" emissão de permissões

Tem de ter permissão para listar as contas de armazenamento. Para obter esta permissão, tem de lhe ser atribuída a função _Reader._

#### <a name="list-storage-account-keys"></a>Chaves da conta de armazenamento de lista

O Storage Explorer também pode usar chaves de conta para autenticar pedidos. Pode ter acesso às chaves da conta através de funções mais poderosas, como o _papel do Contribuinte._

> [!NOTE]
> As chaves de acesso concedem permissões ilimitadas a quem as detém. Por isso, não recomendamos que distribua estas chaves aos utilizadores da conta. Se precisar revogar as teclas de acesso, pode regenerar-as a partir do [portal Azure.](https://portal.azure.com/)

#### <a name="data-roles"></a>Funções de dados

Deve ser-lhe atribuída pelo menos uma função que dá acesso à leitura de dados a partir de recursos. Por exemplo, se quiser listar ou descarregar bolhas, precisará, pelo menos, da função _De Leitore de Dados de Armazenamento Blob._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que preciso de um papel de camada de gestão para ver os meus recursos no Storage Explorer?

O Azure Storage tem duas camadas de acesso: _gestão_ e _dados._ As assinaturas e as contas de armazenamento são acedidas através da camada de gestão. Os contentores, bolhas e outros recursos de dados são acedidos através da camada de dados. Por exemplo, se quiser obter uma lista das suas contas de armazenamento da Azure, envie um pedido para o ponto final de gestão. Se quiser uma lista de recipientes blob numa conta, envie um pedido para o ponto final de serviço apropriado.

As funções Azure podem conceder-lhe permissões para gestão ou acesso à camada de dados. O papel reader, por exemplo, concede acesso apenas de leitura aos recursos de camada de gestão.

Em rigor, a função Reader não fornece permissões de camada de dados e não é necessária para aceder à camada de dados.

O Storage Explorer facilita o acesso aos seus recursos através da recolha das informações necessárias para se ligar aos seus recursos Azure. Por exemplo, para exibir os seus recipientes blob, o Storage Explorer envia um pedido de "lista de contentores" para o ponto final do serviço blob. Para obter esse ponto final, o Storage Explorer procura a lista de subscrições e contas de armazenamento a que tem acesso. Para encontrar as suas subscrições e contas de armazenamento, o Storage Explorer também precisa de acesso à camada de gestão.

Se não tiver um papel que conceda permissões de camada de gestão, o Storage Explorer não consegue obter a informação de que necessita para se ligar à camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não conseguir as permissões de gestão que preciso do meu administrador?

Se quiser aceder a recipientes blob, contentores ou diretórios da ADLS Gen2 ou filas, pode anexar esses recursos utilizando as suas credenciais Azure.

1. Abra o diálogo 'Ligar'.
1. Selecione o tipo de recurso a que pretende ligar.
1. Selecione **Iniciar sação utilizando o Diretório Ativo Azure (Azure AD)**. Selecione **Seguinte**.
1. Selecione a conta de utilizador e o inquilino associado ao recurso a que está a anexar. Selecione **Seguinte**.
1. Introduza o URL no recurso e introduza um nome de exibição único para a ligação. Selecione **Seguinte** e, em **seguida, Conecte-se**.

Para outros tipos de recursos, não temos atualmente uma solução relacionada com o Azure RBAC. Como solução alternativa, pode solicitar um URL SAS e, em seguida, anexar ao seu recurso seguindo estes passos:

1. Abra o diálogo 'Ligar'.
1. Selecione o tipo de recurso a que pretende ligar.
1. Selecione **assinatura de acesso partilhado (SAS)**. Selecione **Seguinte**.
1. Introduza o URL SAS que recebeu e introduza um nome de exibição único para a ligação. Selecione **Seguinte** e, em **seguida, Conecte-se**.
 
Para obter mais informações sobre a fixação aos recursos, consulte [Anexar a um Recurso Individual](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource).

### <a name="recommended-azure-built-in-roles"></a>Funções recomendadas do Azure

Existem vários papéis incorporados Azure que podem fornecer as permissões necessárias para usar o Storage Explorer. Alguns desses papéis são:
- [Proprietário](../../role-based-access-control/built-in-roles.md#owner): Gerir tudo, incluindo acesso a recursos.
- [Contribuinte](../../role-based-access-control/built-in-roles.md#contributor): Gerir tudo, excluindo o acesso aos recursos.
- [Leitor:](../../role-based-access-control/built-in-roles.md#reader)Leia e enuse os recursos.
- [Contribuidor da Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor): Gestão completa das contas de armazenamento.
- [Armazenamento Blob Data Owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Acesso total aos recipientes e dados da bolha de armazenamento Azure.
- [Armazenamento Contribuinte de Dados Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Leia, escreva e elimine os recipientes e bolhas de armazenamento Azure.
- [Armazenamento Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Leia e enuncie os recipientes e bolhas de armazenamento Azure.

> [!NOTE]
> As funções de Proprietário, Contribuinte e Contribuinte de Conta de Armazenamento concedem acesso à chave da conta.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado auto-assinado na cadeia de certificados (e erros semelhantes)

Erros de certificado ocorrem normalmente numa das seguintes situações:

- A aplicação está conectada através de um _proxy transparente._ Isto significa que um servidor (tal como o servidor da empresa) está a intercetar o tráfego HTTPS, desencriptar o tráfego e, em seguida, criptografá-lo usando um certificado auto-assinado.
- Está a executar uma aplicação que está a injetar um certificado TLS/SSL auto-assinado nas mensagens HTTPS que recebe. Exemplos de aplicações que injetam certificados incluem antivírus e software de inspeção de tráfego de rede.

Quando o Storage Explorer vê um certificado auto-assinado ou não fidedquirou, já não sabe se a mensagem HTTPS recebida foi alterada. Se tiver uma cópia do certificado auto-assinado, pode instruir o Explorador de Armazenamento a confiar nele seguindo estes passos:

1. Obtenha uma cópia codificada X.509 (.cer) do certificado.
2. Vá para **editar**  >  **certificados SSL**  >  **Certificados de Importação**, e, em seguida, use o selecionador de ficheiros para encontrar, selecionar e abrir o ficheiro .cer.

Esta questão também pode ocorrer se existirem vários certificados (raiz e intermédio). Para corrigir este erro, ambos os certificados devem ser adicionados.

Se não tem certeza de onde vem o certificado, siga estes passos para encontrá-lo:

1. Instale o OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Qualquer uma das versões luminosas deve ser suficiente.
    * Mac e Linux: Deve ser incluído com o seu sistema operativo.
2. Abrir o sl.
    * Janelas: Abra o diretório de instalação, selecione **/bin/**, e, em seguida, clique duas vezes **openssl.exe**.
    * Mac e Linux: Fugir `openssl` de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`.
4. Procure certificados autoassinados. Se não tem a certeza de quais certificados são auto-assinados, tome nota de qualquer lugar que o sujeito `("s:")` e `("i:")` emitente sejam os mesmos.
5. Quando encontrar certificados auto-assinados, para cada um, copie e cole tudo de (e incluindo) `-----BEGIN CERTIFICATE-----` até um novo ficheiro `-----END CERTIFICATE-----` .cer.
6. Abrir o Explorador de Armazenamento e ir para **editar**  >  **certificados SSL**  >  **Certificados de Importação Certificados**. Em seguida, utilize o selecionador de ficheiros para encontrar, selecionar e abrir os ficheiros .cer que criou.

Se não conseguir encontrar nenhum certificado auto-assinado seguindo estes passos, contacte-nos através da ferramenta de feedback. Também pode abrir o Storage Explorer a partir da linha de comando utilizando a `--ignore-certificate-errors` bandeira. Quando aberto com esta bandeira, o Storage Explorer ignora erros de certificado.

## <a name="sign-in-issues"></a>Problemas de início de sessão

### <a name="understanding-sign-in"></a>Compreensão do sinal

Certifique-se de que leu a documentação [do Iniciar sção no Storage Explorer.](./storage-explorer-sign-in.md)

### <a name="frequently-having-to-reenter-credentials"></a>Frequentemente ter que reentrar credenciais

Ter de reentrar em credenciais é provavelmente o resultado de políticas de acesso condicional definidas pelo seu administrador da AAD. Quando o Storage Explorer lhe pedir para reentrar nas credenciais do painel de contas, deverá ver um **erro de informação...** link. Clique nisso para ver por que razão o Storage Explorer está a pedir-lhe para reentrar nas credenciais. Erros de política de acesso condicional que requerem reentrada de credenciais podem parecer algo parecido com estes:
- O token refresh expirou...
- Deve utilizar a autenticação multi-factor para aceder...
- Devido a uma alteração de configuração feita pelo seu administrador...

Para reduzir a frequência de ter de reentrar em credenciais devido a erros como os acima referidos, terá de falar com o seu administrador da AAD.

### <a name="conditional-access-policies"></a>Políticas de acesso condicional

Se tiver políticas de acesso condicional que necessitem de ser satisfeitas para a sua conta, certifique-se de que está a utilizar o valor **padrão** do Navegador Web para o **Iniciar sção com** a definição. Para obter informações sobre esta definição, consulte ['Alterar onde o sinal de sposição' acontece](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Incapaz de adquirir ficha, inquilino é filtrado

Se vir uma mensagem de erro a dizer que um símbolo não pode ser adquirido porque um inquilino é filtrado, isso significa que está a tentar aceder a um recurso que está num inquilino que filtraste. Para desfillar o arrendatário, vá ao **Painel de Contas** e certifique-se de que a caixa de verificação do inquilino especificado no erro é verificada. Consulte as [contas de Gestão](./storage-explorer-sign-in.md#managing-accounts) para obter mais informações sobre a filtragem dos inquilinos no Storage Explorer.

## <a name="authentication-library-failed-to-start-properly"></a>A biblioteca de autenticação não começou corretamente

Se no arranque vir uma mensagem de erro que diz que a biblioteca de autenticação do Storage Explorer não começou corretamente, certifique-se de que o ambiente de instalação cumpre todos os [requisitos necessários](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites). Não cumprir os pré-requisitos é a causa mais provável desta mensagem de erro.

Se acredita que o seu ambiente de instalação cumpre todos os requisitos, então [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new). Quando abrir o seu problema, certifique-se de incluir:
- O seu so.
- Que versão do Storage Explorer está a tentar usar.
- Se verificou os pré-requisitos.
- [Registos](#authentication-logs) de autenticação de um lançamento infrutífero do Storage Explorer. A verificação da autenticação é ativada automaticamente após este tipo de erro.

### <a name="blank-window-when-using-integrated-sign-in"></a>Janela em branco ao utilizar o insusitado integrado

Se optou por utilizar o **'Sin-in Integrado'** e está a ver uma janela de sinalização em branco, provavelmente terá de mudar para um método de entrada diferente. As caixas de diálogo de sinal em branco ocorrem mais frequentemente quando um servidor ative Directory Federation Services (ADFS) pede ao Storage Explorer que execute um redirecionamento que não é suportado pela Electron.

Para alterar para um método de inscrição diferente, alterando o **Iniciar sção com** a definição em **Definições**  >  **de**  >  **Inscrição de** Aplicação . Para obter informações sobre os diferentes tipos de métodos de inscrição, consulte [alterar onde o sinal acontece](./storage-explorer-sign-in.md#changing-where-sign-in-happens).

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo de reautora ou alteração upn

Se estiver num ciclo de reautorsa ou tiver mudado a UPN de uma das suas contas, então experimente estes passos:

1. Explorador de Armazenamento Aberto
2. Ir para ajudar > reset
3. Certifique-se de que pelo menos a autenticação é verificada. Pode desmarcar outros itens que não pretende reiniciar.
4. Clique no botão Reset
5. Reinicie o Storage Explorer e tente iniciar novamente a sessão.

Se continuar a ter problemas depois de fazer um reset, tente estes passos:

1. Explorador de Armazenamento Aberto
2. Remova todas as contas e, em seguida, feche o Storage Explorer.
3. Elimine a `.IdentityService` pasta da sua máquina. No Windows, a pasta encontra-se a `C:\users\<username>\AppData\Local` . Para Mac e Linux, pode encontrar a pasta na raiz do seu diretório de utilizadores.
4. Se estiver a executar Mac ou Linux, também terá de eliminar a entrada Microsoft.Developer.IdentityService da loja de chaves do seu sistema operativo. No Mac, a loja de chaves é a aplicação *Gnomo Keychain.* No Linux, a aplicação é tipicamente chamada _de Keyring,_ mas o nome pode diferir dependendo da sua distribuição.
6. Reinicie o Storage Explorer e tente iniciar novamente a sessão.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: erros de chavechain ou nenhuma janela de inscrição

O keychain macOS pode por vezes entrar num estado que causa problemas para a biblioteca de autenticação do Explorador de Armazenamento. Para tirar o Keychain deste estado, siga estes passos:

1. Fechar o Explorador de Armazenamento.
2. Abra o chaveiro (prima Comando+Barra espacial, **tecla de** tipo, e prima Enter).
3. Selecione o keychain "login".
4. Selecione o ícone do cadeado para bloquear o Chaveiro. (O cadeado aparecerá bloqueado quando o processo estiver concluído. Pode demorar alguns segundos, dependendo das aplicações que tem abertas).

    ![Ícone de cadeado](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Abra o Explorador de Armazenamento.
6. É solicitado com uma mensagem como "O centro de serviço quer aceder ao Keychain." Introduza a sua palavra-passe de conta de administração Mac e selecione **Sempre Permitir** (ou **Permitir** sempre **permitir** que não esteja disponível).
7. Tente entrar.

### <a name="default-browser-doesnt-open"></a>O navegador predefinido não abre

Se o seu navegador predefinido não abrir ao tentar assinar em experimentar todas as seguintes técnicas:
- Reiniciar explorador de armazenamento
- Abra o seu navegador manualmente antes de iniciar o início do sing-in
- Experimente utilizar **o Início de Sposição Integrado,** consulte ['Alterar onde a sposição' acontece](./storage-explorer-sign-in.md#changing-where-sign-in-happens) para obter instruções sobre como fazê-lo.

### <a name="other-sign-in-issues"></a>Outras questões de inscrição

Se nenhuma das anteriores se aplicar à sua questão de inscrição ou se não conseguir resolver o seu ponto de seditação, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Assinaturas desaparecidas e inquilinos avariados

Se não conseguir recuperar as suas subscrições depois de iniciar sessão com sucesso, experimente os seguintes métodos de resolução de problemas:

* Verifique se a sua conta tem acesso às subscrições que espera. Pode verificar o seu acesso ao entrar no portal para o ambiente Azure que está a tentar utilizar.
* Certifique-se de que assinou através do ambiente Azure correto (Azure, Azure China 21Vianet, Azure Germany, Azure US Government, ou Custom Environment).
* Se estiver atrás de um servidor proxy, certifique-se de que configura corretamente o representante do Explorador de Armazenamento.
* Tente remover e voltar a adicionar a conta.
* Se houver um link "Mais informações" ou "Detalhes de erro", verifique quais as mensagens de erro que estão a ser reportadas para os inquilinos que estão a falhar. Se não tiver a certeza de como responder às mensagens de erro, sinta-se à vontade para [abrir um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Não é possível remover uma conta de armazenamento anexada ou recurso

Se não conseguir remover uma conta ou um recurso de armazenamento anexado através da UI, pode eliminar manualmente todos os recursos anexados eliminando as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Feche o Explorador de Armazenamento antes de eliminar estas pastas.

> [!NOTE]
> Se já importou quaisquer certificados SSL, ressalte o conteúdo do `certs` diretório. Mais tarde, pode utilizar a cópia de segurança para reiportar os seus certificados SSL.

## <a name="proxy-issues"></a>Questões de procuração

O Storage Explorer suporta a ligação aos recursos de armazenamento Azure através de um servidor proxy. Se tiver algum problema de ligação ao Azure via proxy, aqui ficam algumas sugestões.

> [!NOTE]
> O Storage Explorer só suporta a autenticação básica com servidores proxy. Outros métodos de autenticação, como o NTLM, não são suportados.

> [!NOTE]
> O Storage Explorer não suporta ficheiros proxy auto-config para configurar configurações de procuração.

### <a name="verify-storage-explorer-proxy-settings"></a>Verifique as definições de procuração do Explorador de Armazenamento

A **definição de configuração proxy → Proxy → → Proxy** determina de que fonte o Explorador de Armazenamento obtém a configuração de procuração.

Se selecionar "Use variáveis ambientais", certifique-se de definir as `HTTPS_PROXY` variáveis ou `HTTP_PROXY` ambientais (as variáveis ambientais são sensíveis ao caso, por isso certifique-se de definir as variáveis corretas). Se estas variáveis forem indefinidas ou inválidas, o Storage Explorer não utilizará um representante. Reinicie o Storage Explorer depois de modificar quaisquer variáveis ambientais.

Se selecionar "Use as definições de procuração de aplicativos", certifique-se de que as definições de procuração na aplicação estão corretas.

### <a name="steps-for-diagnosing-issues"></a>Passos para diagnosticar problemas

Se ainda estiver a passar por problemas, experimente estes métodos de resolução de problemas:

1. Se conseguir ligar-se à internet sem utilizar o seu representante, verifique se o Storage Explorer funciona sem configurações de procuração ativadas. Se o Storage Explorer se ligar com sucesso, pode haver um problema com o seu servidor proxy. Trabalhe com o seu administrador para identificar os problemas.
2. Verifique se outras aplicações que utilizam o servidor proxy funcionam como esperado.
3. Verifique se pode ligar-se ao portal para o ambiente Azure que está a tentar utilizar.
4. Verifique se pode receber respostas dos seus pontos finais de serviço. Introduza um dos urLs do seu ponto final no seu browser. Se conseguir ligar, deve receber uma `InvalidQueryParameterValue` resposta XML ou semelhante.
5. Verifique se alguém que usa o Storage Explorer com o mesmo servidor proxy pode ligar-se. Se puderem, poderá ter de contactar o administrador do seu servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Uma ferramenta de networking, como o Fiddler, pode ajudá-lo a diagnosticar problemas.

1. Configure a sua ferramenta de rede como um servidor proxy em execução no anfitrião local. Se tiver de continuar a trabalhar por trás de um representante real, poderá ter de configurar a sua ferramenta de rede para se ligar através do representante.
2. Verifique o número de porta utilizado pela sua ferramenta de rede.
3. Configure as definições de procuração do Storage Explorer para utilizar o hospedeiro local e o número de porta da ferramenta de rede (como "localhost:8888").
 
Quando definido corretamente, a sua ferramenta de rede registará pedidos de rede feitos pelo Storage Explorer para os pontos finais de gestão e serviço.
 
Se a sua ferramenta de rede não parecer estar a registar o tráfego do Storage Explorer, tente testar a sua ferramenta com uma aplicação diferente. Por exemplo, insira o URL do ponto final para um dos seus recursos de armazenamento `https://contoso.blob.core.windows.net/` (como) num navegador web, e receberá uma resposta semelhante a:

  ![Exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  A resposta sugere que o recurso existe, mesmo que não possa aceder-lhe.

Se a sua ferramenta de rede apenas mostrar tráfego de outras aplicações, poderá ter de ajustar as definições de procuração no Storage Explorer. Caso contrário, necessitaste de ajustar as definições da tua ferramenta.

### <a name="contact-proxy-server-admin"></a>Administração de servidor de procuração de contacto

Se as definições de procuração estiverem corretas, poderá ter de contactar o administrador do servidor proxy para:

* Certifique-se de que o seu representante não bloqueia o tráfego para a gestão da Azure ou pontos finais de recursos.
* Verifique o protocolo de autenticação utilizado pelo seu servidor proxy. O Storage Explorer suporta apenas protocolos básicos de autenticação. O Storage Explorer não suporta proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Incapaz de Recuperar Crianças"

Se estiver ligado ao Azure através de um representante, verifique se as definições do seu representante estão corretas.

Se o proprietário de uma subscrição ou conta lhe tiver dado acesso a um recurso, verifique se leu ou enumera permissões para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A cadeia de ligação não tem configurações completas

Se receber esta mensagem de erro, é possível que não tenha as permissões necessárias para obter as chaves da sua conta de armazenamento. Para confirmar que este é o caso, vá ao portal e localize a sua conta de armazenamento. Pode fazê-lo clicando no nó para a sua conta de armazenamento e selecionando **Open in Portal**. Depois, vai para a lâmina das **Chaves de Acesso.** Se não tiver permissões para ver as chaves, verá uma mensagem "Não tem acesso". Para contornar este problema, pode obter a chave de conta de outra pessoa e anexar através do nome e da chave, ou pode pedir a alguém um SAS na conta de armazenamento e usá-la para anexar a conta de armazenamento.

Se vir as chaves da conta, preencha um problema no GitHub para que possamos ajudá-lo a resolver o problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Erro ocorreu ao adicionar nova ligação: TypeError: Não é possível ler 'versão' de propriedade indefinida

Se receber esta mensagem de erro quando tentar adicionar uma ligação personalizada, os dados de ligação armazenados no gestor de credenciais locais podem ser corrompidos. Para contornar esta questão, tente eliminar as suas ligações locais corrompidas e, em seguida, adicioná-las novamente:

1. Iniciar o Explorador de Armazenamento. A partir do menu, aceda a  >  **Ferramentas de Desenvolvimento Help Toggle**.
2. Na janela aberta, no **separador Aplicação,** vá ao **Armazenamento Local** (lado esquerdo) > **file://**.
3. Dependendo do tipo de ligação com que está a ter problemas, procure a sua chave e, em seguida, copie o seu valor num editor de texto. O valor é uma variedade de nomes de conexão personalizados, como o seguinte:
    * Contas de armazenamento
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Contentores de blobs
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Partilhas de ficheiros
        * `StorageExplorer_CustomConnections_Files_v1`
    * Filas
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelas
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Depois de guardar os nomes de ligação atuais, desa um valor em Ferramentas de Desenvolvimento para `[]` .

Se quiser preservar as ligações que não são corrompidas, pode usar os seguintes passos para localizar as ligações corrompidas. Se não se importar de perder todas as ligações existentes, pode saltar estes passos e seguir as instruções específicas da plataforma para limpar os seus dados de ligação.

1. A partir de um editor de texto, volte a adicionar cada nome de ligação às Ferramentas do Desenvolvedor e, em seguida, verifique se a ligação ainda está funcionando.
2. Se uma ligação estiver a funcionar corretamente, não está corrompida e pode deixá-la lá com segurança. Se uma ligação não estiver a funcionar, remova o seu valor das Ferramentas de Desenvolvimento e grave-a para que possa adicioná-la mais tarde.
3. Repita até examinar todas as suas ligações.

Depois de analisar todas as suas ligações, para todos os nomes de ligações que não são adicionados de volta, você deve limpar os seus dados corrompidos (se houver algum) e adicioná-los de volta usando os passos padrão no Explorador de Armazenamento:

### <a name="windows"></a>[Windows](#tab/Windows)

1. No menu **Iniciar,** procure **o Gestor credencial** e abra-o.
2. Ir para as **credenciais do Windows**.
3. Sob **as Credenciais Genéricas,** procure entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Elimine estas entradas e reensiem as ligações.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Abra o holofote (Barra de espaço Command+Space) e procure **acesso ao Keychain**.
2. Procure por entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Elimine estas entradas e reensiem as ligações.

### <a name="linux"></a>[Linux](#tab/Linux)

A gestão de credenciais locais varia consoante a distribuição do Linux. Se a sua distribuição Linux não fornecer uma ferramenta GUI incorporada para gestão de credenciais locais, pode instalar uma ferramenta de terceiros para gerir as suas credenciais locais. Por exemplo, você pode usar [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), uma ferramenta GUI de código aberto para gerir credenciais locais linux.

1. Abra a sua ferramenta de gestão de credenciais local e encontre as suas credenciais guardadas.
2. Procure por entradas que tenham a `<connection_type_key>/<corrupted_connection_name>` chave (por exemplo, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Elimine estas entradas e reensiem as ligações.
---

Se ainda encontrar este erro depois de executar estes passos, ou se quiser partilhar o que suspeita ter corrompido as ligações, [abra um problema](https://github.com/microsoft/AzureStorageExplorer/issues) na nossa página do GitHub.

## <a name="issues-with-sas-url"></a>Problemas com o URL SAS

Se estiver a ligar-se a um serviço através de um URL SAS e experimentar um erro:

* Verifique se o URL fornece as permissões necessárias para ler ou listar recursos.
* Verifique se o URL não expirou.
* Se o URL SAS se basear numa política de acesso, verifique se a política de acesso não foi revogada.

Se se ligar acidentalmente utilizando um URL SAS inválido e agora não puder separar-se, siga estes passos:

1. Quando estiver a executar o Storage Explorer, prima F12 para abrir a janela Ferramentas do Desenvolvedor.
2. No **separador Aplicação,** selecione **File:// de Armazenamento Local** na árvore à  >   esquerda.
3. Encontre a chave associada ao tipo de serviço do problemático SAS URI. Por exemplo, se o MAU SAS URI for para um recipiente de bolhas, procure a chave chamada `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. O valor da chave deve ser uma matriz JSON. Encontre o objeto associado ao URI mau e, em seguida, elimine-o.
5. Prima Ctrl+R para recarregar o Explorador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências de Linux

### <a name="snap"></a>Estalar

O Storage Explorer 1.10.0 e mais tarde está disponível como um encaixe da Snap Store. O snap do Storage Explorer instala todas as suas dependências automaticamente, e é atualizado quando uma nova versão do instantâneo está disponível. A instalação do encaixe do Explorador de Armazenamento é o método de instalação recomendado.

O Storage Explorer requer a utilização de um gestor de passwords, que poderá necessitar de ligar manualmente antes de o Explorador de Armazenamento funcionar corretamente. Pode ligar o Storage Explorer ao gestor de passwords do seu sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Arquivo .tar.gz

Também pode descarregar a aplicação como um ficheiro .tar.gz, mas terá de instalar dependências manualmente.

O Storage Explorer, tal como fornecido no .tar.gz download é suportado apenas para as seguintes versões de Ubuntu. O Storage Explorer pode funcionar em outras distribuições do Linux, mas não são oficialmente suportados.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

O Storage Explorer requer que o Núcleo .NET seja instalado no seu sistema. Recomendamos .NET Core 2.1, mas o Storage Explorer também funcionará com 2.2.

> [!NOTE]
> A versão 1.7.0 do Storage Explorer e anteriormente requerem .NET Core 2.0. Se tiver uma versão mais recente de .NET Core instalada, terá de [corrigir o Storage Explorer.](#patching-storage-explorer-for-newer-versions-of-net-core) Se estiver a executar o Storage Explorer 1.8.0 ou mais tarde, precisa de pelo menos .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Descarregue o ficheiro .tar.gz do Storage Explorer.
2. Instale o [prazo de execução do núcleo .NET:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Descarregue o ficheiro .tar.gz do Storage Explorer.
2. Instale o [prazo de execução do núcleo .NET:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Descarregue o ficheiro .tar.gz do Storage Explorer.
2. Instale o [prazo de execução do núcleo .NET:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Muitas bibliotecas necessárias pelo Storage Explorer vêm pré-instaladas com as instalações padrão da Canonical de Ubuntu. Ambientes personalizados podem estar faltando algumas destas bibliotecas. Se tiver problemas no lançamento do Storage Explorer, recomendamos que se certifique de que os seguintes pacotes estão instalados no seu sistema:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer para versões mais recentes de .NET Core

Para o Explorador de Armazenamento 1.7.0 ou mais cedo, poderá ter de corrigir a versão de .NET Core utilizada pelo Storage Explorer:

1. Baixar a versão 1.5.43 do StreamJsonRpc [do NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "Download package" no lado direito da página.
2. Depois de descarregar o pacote, altere a extensão do ficheiro `.nupkg` de `.zip` .
3. Desaperte o pacote.
4. Abra a `streamjsonrpc.1.5.43/lib/netstandard1.1/` pasta.
5. Copiar `StreamJsonRpc.dll` para os seguintes locais na pasta Do Explorador de Armazenamento:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Open In Explorer" do portal Azure não funciona

Se o botão **Open In Explorer** no portal Azure não funcionar, certifique-se de que está a utilizar um navegador compatível. Os seguintes navegadores foram testados para compatibilidade:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Recolha de troncos

Quando reportar um problema ao GitHub, poderá ser-lhe pedido que recolha certos registos para ajudar a diagnosticar o seu problema.

### <a name="storage-explorer-logs"></a>Registos do Explorador de Armazenamento

Começando pela versão 1.16.0, o Storage Explorer regista várias coisas nos seus próprios registos de aplicações. Pode facilmente aceder a estes registos clicando em Help > Open Logs Directory. Por predefinição, o Storage Explorer regista-se a um nível baixo de verbosidade. Para alterar o nível de verbosidade, adicione uma variável ambiental com o nome de `STG_EX_LOG_LEVEL` , e qualquer um dos seguintes valores:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (nível predefinido)
- `verbose`
- `debug`

Os registos são divididos em pastas para cada sessão do Explorador de Armazenamento que executou. Para quaisquer ficheiros de registo que necessite de partilhar, é aconselhável colocá-los num arquivo zip, com ficheiros de diferentes sessões em diferentes pastas.

### <a name="authentication-logs"></a>Troncos de autenticação

Para questões relacionadas com a biblioteca de autenticação do Explorador de Armazenamento ou desinsusação, é provável que necessite de recolher registos de autenticação. Os registos de autenticação são armazenados em:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS e Linux `~/.ServiceHub/logs`

Geralmente, pode seguir estes passos para recolher os registos:

1. Vá a Definições > Iniciar sessão > verificar o registo de autenticação verbose. Se o Storage Explorer não estiver a ser lançado devido a um problema com a sua biblioteca de autenticação, isso será feito por si.
2. Fechar o Explorador de Armazenamento.
1. Opcional/recomendado: limpe os registos existentes da `logs` pasta. Ao fazê-lo, reduzirá a quantidade de informação que tem de nos enviar.
4. Abra o Explorador de Armazenamento e reproduza o seu problema
5. Explorador de armazenamento próximo
6. Feche o conteúdo da `log` pasta.

### <a name="azcopy-logs"></a>Troncos AzCopy

Se tiver problemas em transferir dados, poderá ter de obter os registos AzCopy. Os registos AzCopy podem ser encontrados facilmente através de dois métodos diferentes:
- Para transferências falhadas ainda no Registo de Atividades, clique em "Ir para O Arquivo de Registos AzCopy"
- Para transferências que falharam no passado, vá à pasta de registos AzCopy. Esta pasta pode ser encontrada em:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS e Linux '~/.azcopy

### <a name="network-logs"></a>Registos de rede

Para alguns problemas, terá de fornecer registos das chamadas de rede es feitas pelo Storage Explorer. No Windows, pode fazê-lo utilizando o Fiddler.

> [!NOTE]
> Os vestígios do fiddler podem conter senhas que inseriu/enviou no seu navegador durante a recolha do rastreio. Certifique-se de ler as instruções sobre como higienizar um traço de violino. Não carrede os vestígios do Fiddler para o GitHub. Dir-lhe-ão onde pode enviar o seu rasto de Violino.

Parte 1: Instalar e configurar o violinista

1. Instalar Violinista
2. Iniciar Violino
3. Ir a Ferramentas > Opções
4. Clique no separador HTTPS
5. Certifique-se de que os CONNECTs de captura e o tráfego HTTPS desencriptam são verificados
6. Clique no botão Ações
7. Escolha "Trust Root Certificate" e depois "Sim" no próximo diálogo
8. Clique novamente no botão Ações
9. Escolha "Certificado de raiz de exportação para desktop"
10. Vá para o seu ambiente de trabalho
11. Encontre o ficheiro .cer FiddlerRoot
12. Clique duplo para abrir
13. Vá ao separador "Detalhes"
14. Clique em "Copiar para Arquivar..."
15. No assistente de exportação escolha as seguintes opções
    - Base-64 codificado X.509
    - Para o nome do ficheiro, navegue... a C:\Utilizadores \<your user dir> \AppData\Roaming\StorageExplorer\certs, e depois pode guardá-lo como qualquer nome de ficheiro
16. Feche a janela do certificado
17. Iniciar explorador de armazenamento
18. Ir para editar > Configure Proxy
19. No diálogo, escolha "Use as definições de procuração de aplicativos", e defina o URL http://localhost e a porta para 8888
20. Clique em Ok
21. Reiniciar explorador de armazenamento
22. Devia começar a ver chamadas de rede de um `storageexplorer:` processo que aparece no Fiddler

Parte 2: Reproduzir a questão
1. Feche todas as aplicações que não o Fiddler
2. Limpe o log do Fiddler (ícone X na parte superior esquerda, perto do menu Ver)
3. Opcional/recomendado: deixe o Violinista definir por alguns minutos, se vir chamadas de rede aparecerem, clique com o botão direito e escolha 'Filter Now' > <process name> 'Hide'
4. Iniciar explorador de armazenamento
5. Reproduza o problema
6. Clique em 'Arquivar > Guardar > Todas as Sessões..., guardar em algum lugar que não esquecerá
7. Close Fiddler e Explorador de Armazenamento

Parte 3: Sanitize o traço do Violinista
1. Clique duas vezes no traço do violinista (ficheiro.saz)
2. Imprensa `ctrl`+`f`
3. No diálogo que aparece, certifique-se de que as seguintes opções estão definidas: Pesquisa = Pedidos e respostas, Examine = Cabeçalhos e corpos
4. Procure por quaisquer palavras-passe utilizadas durante a recolha do traço do violinista, quaisquer entradas que sejam realçadas, clique à direita e escolha Remover > Sessões Selecionadas
5. Se definitivamente introduziu senhas no seu navegador enquanto recolhe o rastreio, mas não encontra nenhuma entrada ao usar ctrl+f e não quer alterar as suas palavras-passe/as palavras-passe utilizadas são usadas para outras contas, então sinta-se à vontade para saltar o ficheiro .saz. É melhor prevenir do que remediar. :)
6. Guarde o traço novamente com um novo nome
7. Opcional: apagar o traço original

## <a name="next-steps"></a>Passos seguintes

Se nenhuma destas soluções funcionar para si, pode:
- Criar um pedido de suporte
- [Abra uma edição no GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues) Também pode fazê-lo selecionando a **emissão 'Relatório' para** o botão GitHub no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)