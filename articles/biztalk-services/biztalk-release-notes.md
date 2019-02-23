---
title: Notas de versão para serviços BizTalk do Azure | Documentos da Microsoft
description: Lista os problemas conhecidos para serviços BizTalk do Azure
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 722159b82845eff08e0c302eafd503459941ebe7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734766"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Notas de versão para serviços BizTalk do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

As notas de versão para os serviços BizTalk do Microsoft Azure contêm os problemas conhecidos nesta versão.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>O que há de novo na atualização de Novembro dos serviços BizTalk
* Encriptação em repouso pode ser ativada no Portal de serviços do BizTalk. Ver [ativar a encriptação em inatividade no Portal dos serviços BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Atualizar histórico
### <a name="october-update"></a>Atualização de Outubro
* As contas organizacionais são suportadas:  
  * **Cenário**: Registrou uma implementação de serviço BizTalk utilizando uma conta Microsoft (como user@live.com). Neste cenário, apenas os utilizadores do Microsoft Account podem gerir o serviço BizTalk utilizando o portal dos serviços do BizTalk. Não é possível utilizar uma conta institucional.  
  * **Cenário**: Registrou uma implementação de serviço BizTalk utilizando uma conta organizacional num Azure Active Directory (como user@fabrikam.com ou user@contoso.com). Neste cenário, apenas os utilizadores do Azure Active Directory dentro da mesma organização podem gerir o serviço BizTalk utilizando o portal dos serviços do BizTalk. Não é possível utilizar uma conta Microsoft.  
* Quando cria um BizTalk Service, são automaticamente registrados no Portal de serviços do BizTalk.
  * **Cenário**: Iniciar sessão no Azure, criar um BizTalk Service e, em seguida, selecione **gerir** pela primeira vez. Quando abre o portal dos serviços do BizTalk, o serviço BizTalk registra automaticamente e está pronto para as suas implementações.  
    Ver [Portal dos serviços de registro e a atualizar uma implementação de serviços do BizTalk do BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Atualização de 14 de Agosto
* Contrato e bridge desassociar – negociação de contratos de parceiros e pontes agora estão desassociados no Portal de serviços do BizTalk. Agora, criar contratos e pontes separadamente e, em tempo de execução pontes resolver para um contrato com base nos valores na mensagem de EDI. Ver [criar contratos dos serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [criar uma ponte EDI com o Portal dos serviços BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [criar uma ponte de AS2 utilizando o Portal dos serviços BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx)e [ Como é que pontes de resolver os contratos em tempo de execução?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A opção para criar modelos para contratos foi descontinuada.  
* Para o contrato de lado de envio, agora é possível especificar conjuntos de delimitador diferentes para cada esquema. Esta configuração é especificada nas definições do protocolo de contrato do lado de envio. Para obter mais informações, consulte [Create X12 contrato nos BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [criar um contrato EDIFACT no BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/dn606267.aspx). Duas novas entidades também são adicionadas à API de OM do TPM para a mesma finalidade. Ver [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) e [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Padrão construções XSD, incluindo tipos derivados, agora são suportadas. Ver [XSD de padrão de utilização construções em seus mapas](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [tipos derivados de utilização em cenários de mapeamento e exemplos](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 oferece suporte a novos algoritmos MIC para a assinatura da mensagem e novos algoritmos de criptografia. Ver [criar um contrato de AS2 nos serviços BizTalk do Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  

## <a name="known-issues"></a>Problemas Conhecidos

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividade, após a atualização do Portal dos serviços BizTalk
  Se tiver o Portal de serviços do BizTalk abrir enquanto os serviços do BizTalk é atualizado para distribuir as alterações para o serviço, poderá enfrentar problemas de conectividade com o Portal de serviços do BizTalk.  
  Como solução, pode reiniciar o navegador, eliminar a cache do browser ou iniciar o portal no modo privado.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE do Visual Studio não é possível localizar o artefacto, se clicar num erro ou aviso num projeto de serviços BizTalk
Instale o Visual Studio 2012 Update 3 versão Release Candidate 1 para corrigir o problema.  

### <a name="custom-binding-project-reference"></a>Referência de projeto de ligação personalizada
Considere as seguintes situações com um projeto de serviços BizTalk numa solução do Visual Studio:  

* Na mesma solução do Visual Studio, existe um projeto de serviços do BizTalk e um projeto de ligação personalizada. O projeto de serviço BizTalk possui uma referência a este ficheiro de projeto de ligação personalizada.
* O projeto de serviço BizTalk possui uma referência a um enlace/comportamento personalizado DLL.

"Criar" a solução no Visual Studio com êxito. Em seguida, "Reconstruir" ou "Limpar" a solução. Depois disso, quando reconstruir ou limpa mais uma vez, ocorre o seguinte erro:  
  Não é possível copiar o ficheiro <Path to DLL> para "bin\Debug\FileName.dll". O processo não é possível aceder ao ficheiro 'bin\Debug\FileName.dll', porque está a ser utilizado por outro processo.  

#### <a name="workaround"></a>Solução
* Se [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) é instalado, tem duas opções seguintes:
  
  * Reinicie o Visual Studio, ou
  * Reinicie a solução. Em seguida, execute apenas uma compilação na solução.  
* Se [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) não está instalado, abra o Gerenciador de tarefas, clique em processos de separador, clique no processo de MSBuild.exe e, em seguida, clique no botão de todo o processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Encaminhamento para os pontos finais BasicHttpRelay não é suportada de pontes e o Portal dos serviços BizTalk se carateres não imprimíveis são promovidos como cabeçalhos HTTP
Se utilizar carateres não imprimíveis como parte de propriedades promovidas para mensagens, essas mensagens não podem ser encaminhadas para destinos de reencaminhamento que usar a ligação de BasicHttpRelay. Além disso, as propriedades promovidas que estão disponíveis como parte do controle são codificados de URL para blobs e não codificado para destinos.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN é enviada de forma assíncrona, mesmo se a opção de MDN assíncrona de envio está desmarcada
Considere este cenário – se selecionou o **enviar MDN assíncrono** caixa de verificação e, especifique um URL para enviar o MDN de assíncrono e, em seguida, desmarque a **enviar MDN assíncrono** caixa de verificação mais uma vez, o MDN é ainda enviado para o especificar o URL, apesar de não está selecionada a opção de enviar async MDNs.  
Como solução, tem de limpar o URL especificado antes de se desmarcar a **enviar MDN assíncrono** caixa de verificação e, em seguida, implementar o contrato de AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Carateres de espaço em branco para além de um intercâmbio válido fazer com que uma mensagem vazio para serem enviados para o ponto final de suspensão
Se existirem espaços em branco para além de um segmento IEA, o recurso de desmontagem trata-a como o fim do intercâmbio atual e analisa o próximo conjunto de espaços em branco como uma mensagem seguinte. Uma vez que não se trata de intercâmbio válido, poderá reparar que uma mensagem de êxito é enviada para o destino da rota e uma mensagem vazia é enviada o ponto de extremidade de suspensão.  

### <a name="tracking-in-biztalk-services-portal"></a>Controlo no Portal dos serviços BizTalk
Eventos de rastreio são capturados até o processamento de mensagens EDI e qualquer correlação. Se uma mensagem falhar fora o estágio de protocolo, controlo mostrará a conclusão com êxito. Nesta situação, consulte a secção de registo sob o **detalhes** coluna na **controlo** para obter detalhes de erro.
X12 receber e enviar definições ([Create X12 contrato nos BizTalk Services do Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornecem informações sobre a fase de protocolo.  

### <a name="update-agreement"></a>Atualizar o contrato
O Portal de serviços do BizTalk permite-lhe modificar o qualificador de uma identidade quando é configurado um contrato. Isso pode resultar em Propriedades inconsistentes. Por exemplo, há um contrato com ZZ:1234567 e ZZ:7654321 o qualificador. As definições de perfil de Portal dos serviços BizTalk, pode alterar ZZ:1234567 ser 01:ChangedValue. Abrir o contrato e 01:ChangedValue é apresentado em vez de ZZ:1234567.
Para modificar o qualificador de uma identidade, eliminar o contrato, atualizar **identidades** no perfil de parceiro e, em seguida, recrie o contrato.  

> AZURE. Esse comportamento de aviso afeta X12 e AS2.  
> 
> 

### <a name="as2-attachments"></a>Anexos de AS2
Anexos para AS2 mensagens não são suportadas no envio ou receção. Especificamente, anexos são silenciosamente ignorados e o corpo da mensagem é processado como uma mensagem AS2 regular.  

### <a name="resources-remembering-path"></a>Recursos: Memorizar o caminho
Quando adiciona **recursos**, a janela de caixa de diálogo não se lembrar o caminho utilizado anteriormente para adicionar um recurso. Lembre-se o caminho utilizado anteriormente, tente adicionar o web site do Portal dos serviços BizTalk para **Sites confiáveis** no Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se mudar o nome da entidade de uma ponte e feche o projeto sem a guardar as alterações, abrir a entidade novamente resulta num erro
Considere um cenário na seguinte ordem:  

* Adicionar uma ponte (por exemplo uma ponte de One-way de XML) para um projeto de serviço BizTalk  
* Mudar o nome a ponte, especificando um valor para a propriedade de nome da entidade. Isso muda o nome do ficheiro de .bridgeconfig associado com o nome especificado.  
* Feche o ficheiro de .bcs (fechando a guia no Visual Studio) sem guardar as alterações.  
* Abra o ficheiro de .bcs novamente no Explorador de soluções.  
  Notará que enquanto o ficheiro de .bridgeconfig associado tem o novo nome que especificou, o nome da entidade na superfície de design ainda é o nome antigo. Se tentar abrir a configuração de ponte clicando duas vezes o componente de ponte, receberá o erro seguinte:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Para evitar este cenário, certifique-se de que guardar as alterações depois de mudar o nome das entidades num projeto do BizTalk Service.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Projeto dos BizTalk Services baseia-se com êxito, mesmo que um artefato foi excluído de um projeto do Visual Studio
Considere um cenário onde adiciona um artefacto (por exemplo, um arquivo XSD) para um projeto do BizTalk Service, incluir esse artefato na configuração da Bridge (por exemplo, ao especificá-la como um tipo de mensagem de pedido) e, em seguida, excluí-lo a partir do projeto do Visual Studio. Nesse caso, compilar o projeto não fornecerá nenhum erro, desde que o artefacto eliminado está disponível no disco na mesma localização de onde ele foi incluído no projeto do Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>O projeto de serviço BizTalk não verificar a disponibilidade de esquema, ao configurar os pontes
Num projeto do BizTalk Service, se um esquema que é adicionado ao projeto importa outro esquema, o projeto dos BizTalk Services não verifica se o esquema importado é adicionado ao projeto. Se tentar criar um projeto desse tipo, não obtém os erros de compilação.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A mensagem de resposta de uma ponte de solicitação-resposta XML é sempre de conjunto de carateres UTF-8
Nesta versão, o conjunto de caracteres da mensagem de resposta de uma ponte de solicitação-resposta XML está sempre definido como UTF-8.
  
### <a name="user-defined-datatypes"></a>Tipos de dados definidas pelo utilizador
Os adaptadores do BizTalk Adapter Pack dentro a funcionalidade do BizTalk Adapter Service podem utilizar tipos de dados definidas pelo utilizador para operações de adaptador.
Ao utilizar tipos de dados definidas pelo utilizador, copie os ficheiros (. dll) para a unidade: \Programas\Microsoft Service\BAServiceRuntime\bin\ de adaptador do BizTalk ou para o Global Assembly Cache (GAC) no servidor que aloja o serviço do serviço adaptador do BizTalk. Caso contrário, o seguinte erro pode ocorrer no cliente:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> É recomendado utilizar GACUtil.exe para instalar um ficheiro para a Cache de Assembly Global. GACUtil.exe documenta como utilizar esta ferramenta e as opções de linha de comando do Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar o serviço Web Site da placa de BizTalk
Instalar o **Runtime de serviço adaptador do BizTalk*** cria o **BizTalk Adapter Service** web site no IIS, que contém o **BAService** aplicação. **BAService** aplicativo usa internamente a ligação de reencaminhamento para expandir o alcance do ponto final de serviço no local para a cloud. Para um serviço alojado no local, o ponto de extremidade de reencaminhamento correspondente será registado no barramento de serviço apenas quando o serviço no local é iniciado.  

Se para e inicia um aplicativo, a configuração de inicialização automática de um aplicativo não é honrada. Então, quando **BAService** está parado, tem sempre de reiniciar o **BizTalk Adapter Service** web site em vez disso. Não iniciar ou parar o **BAService** aplicação.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Carateres especiais não devem ser utilizados para nomes de endereço e a entidade de componentes LOB
Não deve utilizar carateres especiais para nomes de endereço e a entidade de componentes LOB. Se fizer isso, obterá um erro ao implementar o projeto de serviço BizTalk. Para determinados carateres como "%", o site do serviço adaptador do BizTalk pode ir num Estado de paragem e terá de iniciá-lo manualmente.

### <a name="test-map-with-get-context-property"></a>Mapa de teste com a propriedade de contexto de Get
Se uma transformação contém um **obter propriedade de contexto** operação do Map, **mapa de teste** irá falhar. Como solução temporária, substitua a **obter propriedade de contexto** mapa operação com uma cadeia de caracteres concatenar mapa de operação que contém dados fictícios. Isto irá preencher o esquema de destino e permitir que testar outras funcionalidades de transformação.

### <a name="test-map-property-does-not-display"></a>Não é apresentada a propriedade de mapa de teste
O **mapa de teste** propriedades não são apresentados no Visual Studio. Isto pode ocorrer se o **propriedades** janela e o **Explorador de soluções** janela não são encaixados em simultâneo. Para resolver este problema, encaixar os **propriedades** e o **Explorador de soluções** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime reformatar pendente está a cinzento
Quando uma operação de mapa de reformatar DateTime é adicionada à superfície de design e configurada, a formato na lista pendente pode estar indisponíveis. Isto pode acontecer se o computador de exibição é definido **médio – 125%** ou **maiores – 150%**. Para resolver, defina a apresentação como **Smaller – 100% (predefinição)** através dos passos abaixo:  

1. Abra o **painel de controlo** e clique em **aparência e personalização**.
2. Clique em **exibição**.
3. Clique em **Smaller – 100% (predefinição)** e clique em **aplicar**.

O **formato** na lista pendente agora deve funcionar conforme esperado.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Contratos de duplicados no Portal de serviços do BizTalk
Considere o seguinte cenário:

1. Crie um contrato com a API OM da gestão de parceiros comerciais.
2. Abra o contrato no Portal de serviços do BizTalk em dois separadores diferentes.
3. Implemente o contrato de ambos os separadores.
4. Como resultado, os contratos de ambos os implementado resultando em entradas duplicadas no Portal de serviços do BizTalk

**Solução**. Abra qualquer um dos contratos de duplicados no Portal de serviços do BizTalk e anular a implementação.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Pontes não utilize o certificado atualizado, mesmo depois de um certificado foi atualizado no armazenamento de artefactos
Considere os seguintes cenários:  

**Cenário 1: A utilização de certificados com base em thumbprint para proteger a transferência de mensagens a partir de uma ponte para um ponto de extremidade de serviço**  
Considere um cenário onde utiliza certificados baseados em thumbprint no seu projeto de serviço BizTalk. Atualizar o certificado no Portal de serviços do BizTalk com o mesmo nome, mas outra impressão digital, mas não a atualizar o projeto de serviço BizTalk da mesma forma. Neste cenário, a ponte poderá continuar a processar as mensagens, uma vez que os dados mais antigos do certificado podem ainda ser na cache do canal. Depois disso, o processamento de mensagens falha.  

**Solução**: Atualizar o certificado no projeto do BizTalk Service e voltar a implementar o projeto.  

**Cenário 2: Utilizar comportamentos com base no nome para identificar certificados para proteger a transferência de mensagens a partir de uma ponte para um ponto de extremidade de serviço**

Considere um cenário em que usar comportamentos com base no nome para identificar certificados no seu projeto de serviço BizTalk. Atualizar o certificado no Portal de serviços do BizTalk, mas não a atualizar o projeto de serviço BizTalk da mesma forma. Neste cenário, a ponte poderá continuar a processar as mensagens, uma vez que os dados mais antigos do certificado podem ainda ser na cache do canal. Depois disso, o processamento de mensagens falha.  

**Solução**: Atualizar o certificado no projeto do BizTalk Service e voltar a implementar o projeto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Pontes continuam a processar mensagens, mesmo quando a base de dados SQL está offline
Os pontes de serviços do BizTalk continuam a processar mensagens durante algum tempo, mesmo que o Microsoft Azure SQL Database (que armazena as informações em execução, como os artefactos implementados e pipelines), está offline. Isso ocorre porque os serviços do BizTalk usa os artefactos em cache e a configuração de ponte.
Se não pretender que o pontes de processar mensagens quando a base de dados SQL está offline, pode utilizar os cmdlets do PowerShell dos serviços BizTalk para parar ou suspender o serviço BizTalk. Ver [amostra de gestão de serviço do Azure BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=329019) para os cmdlets do Windows PowerShell gerir as operações.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Ler a mensagem XML no componente de código personalizado de uma ponte inclui um caráter BOM extra
Considere um cenário em que queira ler uma mensagem XML dentro do código personalizado de uma ponte. Se usar o System.Text.Encoding.UTF8.GetString(bytes) de API do .NET um caráter BOM extra está incluído na saída no início da mensagem. Então, se não quiser que a saída inclui o caráter BOM extra, tem de utilizar ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Enviar mensagens para uma ponte usando o WCF não dimensionar
As mensagens enviadas para uma ponte usando o WCF não dimensionar. Em vez disso, deve usar HttpWebRequest, se pretender que um cliente dimensionável.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ATUALIZAÇÃO: Erro do fornecedor do token após a atualização da pré-visualização de serviços do BizTalk para disponibilidade geral (GA)
Existe um EDI ou o contrato de AS2 com lotes Active Directory. Quando o serviço BizTalk é atualizado de pré-visualização para disponibilidade geral, pode ocorrer o seguinte:

* Erro: O fornecedor do token não foi possível fornecer um token de segurança. Fornecedor do token devolvido a mensagem: Não foi possível resolver o nome remoto.
* Tarefas do batch são canceladas.

**Solução**: Depois do serviço BizTalk é atualizado para disponibilidade geral (GA), volte a implementar o contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ATUALIZAÇÃO: Caixa de ferramentas mostra os ícones de ponte antigo depois de atualizar o SDK dos serviços do BizTalk
Depois de atualizar uma versão anterior do SDK de serviços do BizTalk, que tinha que representa os pontes de ícones antigos, a caixa de ferramentas continua mostrar os ícones antigos de ponte. No entanto, se adicionar uma ponte para a superfície de desenho de projeto do BizTalk Service, a superfície de mostra o ícone de novo.  

**Solução**. Pode contornar este problema ao eliminar os ficheiros. TBD <system drive>: \Users\<utilizador > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ATUALIZAÇÃO: Atualização do BizTalk Portal de pré-visualização em DG poderá mostrar um erro que indica que a capacidade EDI não está disponível
Se tiver sessão iniciada no Portal de serviços do BizTalk enquanto os serviços do BizTalk é atualizado a partir da pré-visualização para disponibilidade geral, poderá receber o erro seguinte no portal:  

Esta capacidade não está disponível como parte nesta edição dos serviços BizTalk do Microsoft Azure. Para utilizar estas capacidades de mudar para uma edição adequada.  

**Resolução**: Terminar sessão no portal, feche e abra o browser e, em seguida, inicie sessão no portal.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ATUALIZAÇÃO: Novos dados de controlo não aparecem depois dos serviços BizTalk é atualizado para disponibilidade geral
Assumir um cenário em que tem uma ponte XML implementada na subscrição de pré-visualização do BizTalk Services. Enviar mensagens para a ponte e o controle de dados correspondente está disponível no Portal de serviços do BizTalk. Agora, se os bits de tempo de execução do Portal dos serviços BizTalk e os serviços BizTalk são atualizados para disponibilidade geral e enviar uma mensagem para o mesmo ponto final ponte implementado anteriormente, os dados de controle não aparecem para mensagens enviadas depois da atualização.  

### <a name="pipelines-versus-bridges"></a>Pipelines versus pontes
Em todo este documento, o termo 'pipelines' e "pontes" são utilizados alternadamente. Ambos significam, essencialmente, a mesma coisa, o que é, uma unidade de processamento de mensagem implementada nos serviços BizTalk.  

### <a name="concepts"></a>Conceitos
[Serviços BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

