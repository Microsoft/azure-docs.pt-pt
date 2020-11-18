---
title: Importar informações sobre ameaças para Azure Sentinel Microsoft Docs
description: Use feeds de inteligência de ameaça em Azure Sentinel para analisar dados, detetar ameaças e gerar alertas e incidentes. Visualizar informações de inteligência de ameaças com livros de trabalho.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: bde11c8e06891025be96810acf6d87952a3d8d2f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660785"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importar informações sobre ameaças para o Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Introdução à inteligência ameaçadora

Cyber Threat Intelligence (CTI) é uma informação que descreve ameaças conhecidas ou potenciais a sistemas e utilizadores. Este tipo de informação assume muitas formas, desde relatórios escritos que detalham as motivações, infraestruturas e técnicas de um determinado ator ameaça, até observações específicas de endereços IP, domínios e hashes de ficheiros associados a ameaças cibernéticas. O CTI é usado pelas organizações para fornecer contexto essencial a atividades incomuns, para que o pessoal de segurança possa rapidamente tomar medidas para proteger as suas pessoas e bens. O CTI pode ser obtido a partir de muitos lugares, tais como feeds de dados de código aberto, comunidades de partilha de informações ameaçadoras, feeds de inteligência comercial e informações locais recolhidas no decorrer de investigações de segurança dentro de uma organização.

Dentro de uma solução de Gestão de Informação de Segurança e Eventos (SIEM), como o Azure Sentinel, a forma mais utilizada de CTI são indicadores de ameaça, muitas vezes referidos como Indicadores de Compromisso ou IoCs. Os indicadores de ameaça são dados que associam observações como URLs, hashes de ficheiros ou endereços IP com atividades de ameaça conhecidas, tais como phishing, botnets ou malware. Esta forma de inteligência de ameaça é muitas vezes chamada de inteligência de ameaça tática porque pode ser aplicada a produtos de segurança e automação em larga escala para proteger e detetar potenciais ameaças a uma organização. No Azure Sentinel, pode utilizar indicadores de ameaça para ajudar a detetar atividades maliciosas observadas no seu ambiente e fornecer contexto aos investigadores de segurança para ajudar a informar as decisões de resposta.

Pode integrar a inteligência de ameaças (TI) no Azure Sentinel através das seguintes atividades:

- Utilize **conectores de dados** em várias plataformas TI para [importar inteligência de ameaça](./connect-threat-intelligence.md) para Azure Sentinel.
- Ver e gerir a inteligência de ameaças importada em **Logs** e na nova área de Inteligência de **Ameaça** de Azure Sentinel.
- Use os modelos de regras **de Analytics** incorporados para gerar alertas de segurança e incidentes usando a sua inteligência de ameaça importada.
- Visualize informações chave sobre a sua inteligência de ameaça em Azure Sentinel com o **livro de inteligência ameaça**.

A Threat Intelligence também fornece um contexto útil dentro de outras experiências do Azure Sentinel, como **a Caça** e **Os Cadernos,** e embora não esteja abrangida por este artigo, estas experiências são abordadas [neste grande post de blog por Ian Hellen em Jupyter Notebooks in Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), que abrange o uso de CTI dentro de Cadernos.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Conectores de dados Azure Sentinel para inteligência de ameaça

Tal como todos os outros dados do evento no Azure Sentinel, os indicadores de ameaça são importados usando conectores de dados. Existem dois conectores de dados em Azure Sentinel fornecidos especificamente para indicadores de ameaça, **Threat Intelligence - TAXII** e **Threat Intelligence Platforms**. Pode utilizar o conector de dados sozinho, ou ambos os conectores em conjunto, dependendo de onde a sua organização fornece indicadores de ameaça. Vamos falar sobre cada um dos conectores de dados.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Adicionar indicadores de ameaça ao Azure Sentinel com o conector de dados das Plataformas de Inteligência de Ameaça

Muitas organizações utilizam soluções da plataforma de inteligência de ameaças (TIP) para agregar os feeds de indicadores de ameaça de várias fontes, para curar os dados dentro da plataforma e, em seguida, escolher quais indicadores de ameaça aplicar a várias soluções de segurança, como dispositivos de rede, soluções avançadas de proteção de ameaças ou SIEMs como o Azure Sentinel. Se a sua organização utilizar uma solução TIP integrada, como MISP, Anomali ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Threat Intelligence Platform, ou MineMeld da Palo Alto Networks, o conector de **dados das Plataformas de Inteligência de Ameaças** permite-lhe utilizar o seu TIP para importar indicadores de ameaça para o Azure Sentinel. Uma vez que o conector trabalha com a [API de Segurança de Gráficos](/graph/api/resources/tiindicator) da Microsoft para o conseguir, o conector também pode ser utilizado por qualquer plataforma de inteligência de ameaça personalizada para aproveitar a API dos tiIndicators para enviar indicadores para O Azure Sentinel (e para outras soluções de segurança da Microsoft como o Defender ATP).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Caminho de importação de inteligência de ameaça":::

Siga estes passos para importar indicadores de ameaça para a Azure Sentinel a partir da sua solução integrada de TIP ou inteligência de ameaça personalizada:

1. Obtenha um ID de aplicação e segredo de cliente do seu Diretório Ativo Azure

1. Insira esta informação na sua solução TIP ou aplicação personalizada

1. Ativar o conector de dados das Plataformas de Inteligência de Ameaça em Azure Sentinel

Aqui está um olhar detalhado sobre cada um destes passos.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Obtenha um ID de aplicação e segredo de cliente do seu Diretório Ativo Azure

Quer esteja a trabalhar com uma DICA ou com uma solução personalizada, a API dos tiIndicators requer algumas informações básicas para ligar e enviar indicadores de ameaça. As três informações que precisa de obter são:
- ID da Aplicação (cliente)
- ID do Diretório (inquilino)
- Segredo do cliente

Esta informação vem sempre do seu Diretório Ativo Azure através de um processo chamado **Registo de Aplicações** que inclui os três passos seguintes:
- Register an app with Azure Active Directory (Registar uma aplicação no Azure Active Directory)
- Especificar as permissões exigidas pela app para ligar à API do Microsoft Graph tiDicators e enviar indicadores de ameaça
- Obtenha o consentimento da sua organização para conceder estas permissões a esta aplicação.  

**Registar uma aplicação com o Azure Active Directory**

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Ative Directory.**

1. Selecione **registos** de aplicações no menu e selecione **Novo registo.**

1. Escolha um nome para o seu registo de inscrição, selecione o botão de rádio **single tenant** e selecione **Register**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registar uma aplicação":::

1. A partir do ecrã resultante, copie os valores de ID de **Aplicação (cliente)** e **Diretório (inquilino).** Estas são as duas primeiras informações que mais tarde necessitará para configurar o seu TIP ou solução personalizada para enviar indicadores de ameaça ao Azure Sentinel.

**Especificar as permissões exigidas pela aplicação**

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Ative Directory.**

1. Selecione Registos de **Aplicações** no menu e selecione a sua aplicação recém-registada.

1. Selecione **permissões API** no menu e clique no botão Adicionar um botão **de permissão.**

1. Na página **Select aPI,** selecione **o Microsoft Graph** para escolher entre uma lista de permissões do Microsoft Graph.

1. Quando questionado **Sobre que tipo de permissões a sua aplicação requer?** Selecione **permissões de aplicação**. Este é o tipo de permissões utilizadas por aplicações autenticadas com ID de aplicações e Segredos de Aplicação (Chaves API).

1. Selecione **ThreatIndicators.ReadWrite.OwnedBy** e **selecione Adicionar permissões** para adicionar esta permissão à lista de permissões da sua aplicação.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Especificar permissões":::

**Obtenha o consentimento da sua organização para conceder estas permissões**

1. Para conceder o seu consentimento, precisa de um Administrador Global do Diretório Ativo Azure para selecionar o **consentimento administrativo de Grant para o** botão de inquilino na página de permissões API da sua aplicação. Se não tiver a função de Administrador Global na sua conta, este botão não estará disponível, e terá de pedir a um Administrador Global da sua organização para executar este passo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Conceder consentimento":::

1. Uma vez concedido o consentimento à sua aplicação, deverá ver uma marca de verificação verde em **Status**.
 
Agora que a sua aplicação foi registada e foram concedidas permissões, pode obter a última coisa na sua lista - um segredo de cliente para a sua app.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Ative Directory.**

1. Selecione Registos de **Aplicações** no menu e selecione a sua aplicação recém-registada.

1. Selecione **Certificados & segredos** do menu e clique no botão secreto do **Novo cliente** para obter um segredo (chave API) para a sua aplicação.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Obter segredo do cliente":::

1. Clique no botão **Adicionar** e **certifique-se de copiar o segredo do cliente,** uma vez que não pode recuperar este segredo novamente se navegar para longe desta página. Vai precisar deste valor quando configurar a sua DICA ou solução personalizada.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Insira esta informação na sua solução TIP ou aplicação personalizada

Tem agora as três informações necessárias para configurar o seu TIP ou solução personalizada para enviar indicadores de ameaça ao Azure Sentinel. 
- ID da Aplicação (cliente)
- ID do Diretório (inquilino)
- Segredo do cliente

Introduza estes valores na configuração do seu TIP integrado ou solução personalizada sempre que necessário, e os indicadores de ameaça serão enviados através do Microsoft Graph tiIndicators API direcionados para o Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Ativar o conector de dados das Plataformas de Inteligência de Ameaça em Azure Sentinel

O último passo no processo de integração é permitir o conector de **dados** das Plataformas de Inteligência de Ameaça em Azure Sentinel. Este é o passo que importa os indicadores de ameaça enviados do seu TIP ou solução personalizada através da API do Microsoft Graph tiDdicators para Azure Sentinel. Estes indicadores estarão disponíveis para todos os espaços de trabalho do Azure Sentinel para a sua organização. Siga estes passos para permitir o conector de dados das Plataformas de Inteligência de Ameaças para cada espaço de trabalho:

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual pretende importar os indicadores de ameaça enviados da sua DICA ou solução personalizada.

1. Selecione **os conectores** de dados do menu, selecione Plataformas de Inteligência de **Ameaças** na galeria de conectores e clique no botão de página do **conector Open.**

1. Como já completou o registo da aplicação e configurado o seu TIP ou solução personalizada para enviar indicadores de ameaça, o único passo que resta é clicar no botão **Ligar.**

Dentro de poucos minutos, os indicadores de ameaça devem começar a fluir para este espaço de trabalho do Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Adicionar indicadores de ameaça ao Azure Sentinel com a Threat Intelligence - CONECTOR DE DADOS TAXII

O padrão da indústria mais amplamente adotado para a transmissão de inteligência de ameaças é uma [combinação do formato de dados STIX e do protocolo TAXII](https://oasis-open.github.io/cti-documentation/). Se a sua organização obtiver indicadores de ameaça a partir de soluções que suportem a versão atual STIX/TAXII (2.0 ou 2.1), pode utilizar o conector de dados **Threat Intelligence - TAXII** para trazer os seus indicadores de ameaça para o Azure Sentinel. O conector de dados Da Ameaça - TAXII permite a um cliente taxii incorporado em Azure Sentinel importar inteligência de ameaça a partir de servidores TAXII 2.x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Caminho de importação TAXII":::
 
Siga estes passos para importar indicadores de ameaça formatados STIX para Azure Sentinel de um servidor TAXII:

1. Obtenha o servidor TAXII API Root and Collection ID

1. Ativar a Inteligência de Ameaça - Conector de dados TAXII em Azure Sentinel

Agora vamos dar uma olhada detalhada em cada um destes passos.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Obtenha o servidor TAXII API Root and Collection ID

Os servidores TAXII 2.x anunciam a API Roots, que são URLs que acolhem Coleções de inteligência de ameaças. Na maioria das vezes, a Raiz da API pode ser obtida através da página de documentação do fornecedor de inteligência de ameaça que hospeda o servidor TAXII. No entanto, às vezes a única informação anunciada é um URL conhecido como Discovery Endpoint. Se for esse o caso, é fácil encontrar a Raiz API utilizando o Discovery Endpoint. Se já conhece os IDs de raiz e recolha do servidor TAXII com os quais pretende trabalhar, sinta-se à vontade para saltar para a secção seguinte, Ative o conector de **dados TAXII - TAXII em Azure Sentinel**.

Vejamos um exemplo real de como utilizar um utilitário de linha de comando simples chamado [cURL](https://en.wikipedia.org/wiki/CURL), que é fornecido no Windows e na maioria das distribuições linux, para descobrir a Raiz da API e navegar nas Coleções de um servidor TAXII a partir do ponto final da descoberta. Para este exemplo, usaremos o ponto final de descoberta do servidor [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0.

1.  A partir de um navegador, navegue até ao ponto final de descoberta do [servidor ThreatStream TAXII 2.0](https://limo.anomali.com/taxii) para recuperar a Raiz da API. Autenticar com o utilizador e a `guest` palavra-passe.

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Utilize o utilitário cURL e a Raiz API https://limo.anomali.com/api/v1/taxii2/feeds/) (a partir do passo anterior para navegar na lista de IDs de cobrança alojados na Raiz da API

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Tem agora toda a informação necessária para ligar o Azure Sentinel a uma ou mais coleções de servidores TAXII fornecidas pela Anomali Limo.

| **Raiz API** ( Raiz de API )https://limo.anomali.com/api/v1/taxii2/feeds/) | ID da coleção |
| ------------------------------------------------------------ | ------------: |
| **Tanque Phish**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Domínios do ransomware Abuse.ch**                              | 136           |
| **IPs de digitalização DShield**                                     | 150           |
| **Lista de domínio de malware - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Ameaças Emergentes C&C Servidor**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Cibercrime**                                               |  41           |
| **Ameaças Emergentes - Comprometidos**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Ativar a Inteligência de Ameaça - Conector de dados TAXII em Azure Sentinel

Para importar indicadores de ameaça para Azure Sentinel a partir de um servidor TAXII, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual pretende importar indicadores de ameaça a partir do servidor TAXII.

1. Selecione **os conectores** de dados do menu, selecione **Threat Intelligence - TAXII** a partir da galeria de conectores e clique no botão de página do **conector Open.**

1. Digite um **nome** para esta Coleção de servidor TAXII, **URL raiz API,** **ID de recolha,** **nome de utilizador** (se necessário) e **Palavra-passe** (se necessário) e clique no botão **Adicionar.**

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Configure servidores TAXII":::
 
Deverá receber a confirmação de que uma ligação ao servidor TAXII foi estabelecida com sucesso, podendo repetir o passo (4) acima das tantas vezes que o desejado para ligar a várias Coleções dos mesmos ou diferentes servidores TAXII.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Veja os seus indicadores de ameaça no Azure Sentinel

Agora que importou com sucesso indicadores de ameaça para o Azure Sentinel usando as Plataformas de Inteligência de Ameaças e/ou o conector de dados **da ThreatIntelligence** **Intelligence** em **Logs,** que é onde todos os seus **dados** de eventos do Azure Sentinel são armazenados. Esta tabela é a base para consultas realizadas por outras funcionalidades do Azure Sentinel, como Analytics e Workbooks. Aqui está como encontrar e ver os seus indicadores de ameaça na tabela **ThreatIntelligenceIndicator.**

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual importou indicadores de ameaça usando o conector de dados de inteligência de ameaça.

1. Selecione **Registos** na secção **Geral** do menu Azure Sentinel.

1. A tabela **ThreatIntelligenceIndicator** está localizada sob o grupo **Azure Sentinel.**

1. Selecione o ícone **de dados de pré-visualização** (o olho) ao lado do nome da tabela e selecione o botão **'Ver'** para executar uma consulta que mostrará registos a partir desta tabela.

Os seus resultados devem ser semelhantes ao indicador de ameaça da amostra apresentado abaixo:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Dados de consulta de amostras":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Gerencie os seus indicadores de ameaça na nova área de Inteligência de Ameaças do Azure Sentinel

Com a nova área de **Inteligência de Ameaças,** acessível a partir do menu Azure Sentinel, também pode ver, classificar, filtrar e pesquisar os seus indicadores de ameaça importados sem sequer escrever uma consulta **de Logs.** Esta nova área também permite criar indicadores de ameaça diretamente dentro da interface Azure Sentinel, bem como executar tarefas administrativas comuns de inteligência de ameaça, como a marcação de indicadores e a criação de novos indicadores relacionados com investigações de segurança.
Vejamos duas das tarefas mais comuns, criando novos indicadores de ameaça e indicadores de marcação para facilitar o agrupamento e a referência.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual importou indicadores de ameaça usando o conector de dados de inteligência de ameaça.

1. Selecione **A Informação** sobre Ameaças da secção de Gestão de Ameaças do menu Azure Sentinel.

1. Selecione o botão **Adicionar novo** no menu superior da página.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Adicione um novo indicador de ameaça" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Escolha o tipo indicador e, em seguida, complete os campos necessários marcados com um asterisco vermelho (*) no painel **indicador Novo.**

1. Selecione **Aplicar**. O indicador é adicionado à grelha indicadores e é também enviado para a tabela ThreatIntelligenceIndicator em **Logs**.

A marcação de indicadores de ameaça é uma forma fácil de os agrupar para facilitar a sua descoberta. Normalmente, você pode aplicar uma etiqueta em indicadores relacionados com um incidente particular, ou em indicadores que representam ameaças de um determinado ator conhecido ou uma campanha de ataque bem conhecida. Pode marcar indicadores de ameaça individualmente ou indicadores multi-selecionados e marcá-los todos de uma só vez. Mostrado abaixo é um exemplo de marcação de múltiplos indicadores com um ID incidente. Uma vez que a marcação é de forma livre, uma prática recomendada é criar convenções de nomeação padrão para etiquetas indicadoras de ameaça. Pode aplicar várias etiquetas em cada indicador.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Aplicar etiquetas em indicadores de ameaça" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analytics coloca os seus indicadores de ameaça a trabalhar na deteção de potenciais ameaças

Tens os teus indicadores de ameaça alimentados com o Azure Sentinel; você viu como vê-los e geri-los; Agora vê o que podem fazer por ti. O caso de utilização mais importante para indicadores de ameaças em soluções SIEM como o Azure Sentinel é o poder das regras de análise.  Estas regras baseadas em indicadores comparam eventos brutos das suas fontes de dados com os seus indicadores de ameaça para detetar ameaças de segurança na sua organização. No Azure Sentinel **Analytics,** cria regras de análise que funcionam de forma programada e geram alertas de segurança. As regras são impulsionadas por consultas, juntamente com configurações que determinam com que frequência a regra deve ser executada, que tipo de resultados de consulta devem gerar alertas de segurança, e quaisquer respostas automatizadas para desencadear quando os alertas são gerados.

Embora possa sempre criar novas regras de análise a partir do zero, o Azure Sentinel fornece um conjunto de modelos de regras incorporados, criados por engenheiros de segurança da Microsoft, que pode usar como-é ou modificar para satisfazer as suas necessidades. Pode identificar facilmente os modelos de regra que utilizam indicadores de ameaça, uma vez que todos eles são intitulados a partir de "**mapa ti...".** Todos estes modelos de regra funcionam da mesma forma, sendo a única diferença que tipo de indicadores de ameaça são usados (domínio, e-mail, hash de ficheiro, endereço IP ou URL) e qual o tipo de evento a comparar. Cada modelo lista as fontes de dados necessárias para que a regra funcione, para que possa ver de novo se tem os eventos necessários já importados em Azure Sentinel.

Vamos olhar para um destes modelos de regras e percorrer como ativar e configurar a regra para gerar alertas de segurança usando os indicadores de ameaça que importou para o Azure Sentinel. Para este exemplo, usaremos o modelo de regra chamado **ti map IP entidade para AzureActivity**. Esta regra corresponderá a qualquer indicador de ameaça do tipo endereço IP com todos os eventos de Atividade Azure. Quando um fósforo for encontrado, será gerado um **alerta,** bem como um **incidente** correspondente para investigação pela sua equipa de operações de segurança. Esta regra de análise só funcionará com sucesso se tiver permitido que um ou ambos os conectores de dados da **Inteligência de Ameaça** (para importar indicadores de ameaça) e o conector de dados da Atividade **Azure** (para importar os eventos de nível de subscrição do Azure).

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual importou indicadores de ameaça utilizando os conectores de dados de Inteligência de **Ameaça** e dados de atividade do Azure utilizando o conector de dados da **Atividade Azure.**

1. Selecione **Analytics** a partir da secção **configuração** do menu Azure Sentinel.

1. Selecione o **separador 'Modelos de Regra'** para ver a lista dos modelos de regras de análise disponíveis.

1. Navegue para a regra intitulada **TI mapIP entidade para a AzureActivity** e certifique-se de que ligou todas as fontes de dados necessárias, conforme mostrado abaixo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Fontes de dados necessárias":::

1. Selecione esta regra e selecione o botão **'Criar regra'.** Isto abre um feiticeiro para configurar a regra. Complete as definições aqui e selecione o **seguinte: Definir lógica de regra >** botão.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Criar regra de análise":::

1. A parte lógica da regra do assistente contém:
    - A consulta que será usada na regra.

    - Mapeamentos de entidades, que dizem ao Azure Sentinel como reconhecer entidades como Contas, endereços IP e URLs, para que **incidentes** e **investigações** entendam como trabalhar com os dados em quaisquer alertas de segurança gerados por esta regra.

    - O horário para executar esta regra.

    - O número de resultados de consulta necessários antes de um alerta de segurança é gerado.

    As definições predefinidos no modelo são:
    - Corra uma vez por hora.

    - Combine quaisquer indicadores de ameaça de endereço IP da tabela **ThreatIntelligenceIndicator** com qualquer endereço IP encontrado na última hora de eventos a partir da tabela **AzureActivity.**

    - Gere um alerta de segurança se os resultados da consulta forem superiores a zero, o que significa que se forem encontrados fósforos.

Pode deixar as definições predefinidos ou alterar qualquer uma delas para satisfazer os seus requisitos. Quando terminar, selecione o **seguinte: Botão de resposta automatizada >**

1. Este passo do assistente permite-lhe configurar qualquer automatização que gostaria de ativar quando um alerta de segurança é gerado a partir desta regra de análise. A automatização em Azure Sentinel é feita com **playbooks,** alimentados por Azure Logic Apps. Para saber mais, consulte este [Tutorial: Crie respostas automáticas de ameaças em Azure Sentinel](./tutorial-respond-threats-playbook.md). Para este exemplo, apenas selecionaremos o **seguinte: Rever >** botão para continuar.

1. Este último passo valida as definições da sua regra. Quando estiver pronto para ativar a regra, selecione o botão **Criar** e está acabado.

Agora que ativou a sua regra de análise, pode encontrar a sua regra ativada no separador **regras Ative** da secção **Analytics** do Azure Sentinel. Pode editar, ativar, desativar, duplicar ou eliminar a regra ativa a partir daí. A nova regra é executada imediatamente após a ativação, e a partir daí será executado na sua programação definida.

De acordo com as definições predefinidos, cada vez que a regra for executado no seu horário, quaisquer resultados encontrados gerarão um alerta de segurança. Os alertas de segurança em Azure Sentinel podem ser vistos na secção **de Registos** de Azure Sentinel, na tabela **SecurityAlert** sob o grupo **Azure Sentinel.**

No Azure Sentinel, os alertas gerados pelas regras de análise também geram incidentes de segurança que podem ser **encontrados** em Incidentes sob **Gestão de Ameaças** no menu Azure Sentinel. Incidentes são o que as suas equipas de operações de segurança vão triagem e investigação para determinar as ações de resposta apropriadas. Pode encontrar informações detalhadas neste [Tutorial: Investigue incidentes com a Azure Sentinel.](./tutorial-investigate-cases.md)

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Os livros fornecem informações sobre a sua inteligência de ameaça

Finalmente, pode utilizar um Livro de Trabalho do Azure Sentinel para visualizar informações chave sobre a sua inteligência de ameaça em Azure Sentinel, e pode facilmente personalizar os livros de trabalho de acordo com as necessidades do seu negócio.
Vamos percorrer como encontrar o livro de inteligência de ameaça fornecido no Azure Sentinel, e também mostraremos como fazer edições no livro para personalizá-lo.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue para o serviço **Azure Sentinel.**

1. Escolha o **espaço de trabalho** para o qual importou indicadores de ameaça usando o conector de dados de inteligência de ameaça.

1. Selecione **Livros de Trabalho** da secção de **gestão** de ameaças do menu Azure Sentinel.

1. Navegue para o livro intitulado **Threat Intelligence** e verifique se tem dados na tabela **ThreatIntelligenceIndicator,** como mostrado abaixo.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Verificar os dados":::
 
1. Selecione o botão **Guardar** e escolha uma localização Azure para armazenar o livro. Este passo é necessário para modificar o livro de qualquer forma e guardar as suas alterações.

1. Selecione agora o botão **'Ver' guardado** para abrir o livro para visualização e edição.

1. Deve agora ver as tabelas predefinidos fornecidas pelo modelo. Agora vamos fazer algumas alterações a uma das tabelas. Selecione o botão **Editar** na parte superior da página para introduzir o modo de edição para o livro.

1. Vamos adicionar um novo gráfico de indicadores de ameaça por tipo de ameaça. Percorra a parte inferior da página e selecione Add Consulta.

1. Adicione o seguinte texto à caixa de texto de consulta de **registo do log analytics:**
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Na **versão** drop-down, selecione **a tabela de barras**.

1. Selecione o botão **de edição Feito.** Criaste um novo gráfico para o teu livro.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Gráfico de barras":::

Os livros de trabalho fornecem poderosos dashboards interativos que lhe dão informações sobre todos os aspetos do Azure Sentinel. Há muito que você pode fazer com livros de trabalho, e embora os modelos fornecidos sejam um ótimo ponto de partida, você provavelmente vai querer mergulhar e personalizar estes modelos, ou criar novos dashboards combinando muitas fontes de dados diferentes para que você possa visualizar seus dados de formas únicas. Uma vez que os livros de Azure Sentinel são baseados em livros do Azure Monitor, já existe documentação extensa disponível, e muitos mais modelos. Um ótimo lugar para começar é este artigo sobre como [criar relatórios interativos com os livros de trabalho do Azure Monitor.](../azure-monitor/platform/workbooks-overview.md) 

Há também uma rica comunidade de livros do [Azure Monitor no GitHub](https://github.com/microsoft/Application-Insights-Workbooks) onde você pode baixar modelos adicionais e contribuir com seus próprios modelos.

## <a name="next-steps"></a>Próximos passos
Neste documento, soubeste das capacidades de inteligência de ameaça do Azure Sentinel e da nova lâmina da Inteligência de Ameaça. Para obter orientações práticas sobre a utilização das capacidades de inteligência de ameaça de Azure Sentinel, consulte os seguintes artigos:

- [Ligue os dados da inteligência](./connect-threat-intelligence.md) de ameaça ao Azure Sentinel.
- Crie alertas [incorporados](./tutorial-detect-threats-built-in.md) ou [personalizados](./tutorial-detect-threats-custom.md) e [investigue](./tutorial-investigate-cases.md) incidentes em Azure Sentinel.