---
title: Utilizar o Azure AD Connect Health com o AD FS | Microsoft Docs
description: Esta é a página do Azure AD Connect Health acerca de como monitorizar a infraestrutura do AD FS no local.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70fb463b1ac8664838404a7dfcd0380da8f3358d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889499"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Monitorizar o AD FS utilizando o Azure AD Connect Health
A seguinte documentação é específica para monitorizar a infraestrutura do AD FS com o Azure AD Connect Health. Para obter informações sobre a monitorização do Azure AD Connect (Sincronização) com o Azure AD Connect Health, consulte [Utilizar Azure AD Connect Health para Sincronização](how-to-connect-health-sync.md). Além disso, para obter informações sobre a monitorização dos Serviços de Domínio do Active Directory com o Azure AD Connect Health, consulte [Utilizar Azure AD Connect Health com AD DS](how-to-connect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertas do AD FS
A secção Alertas do Azure AD Connect Health apresenta a lista de alertas ativos. Cada alerta inclui informações relevantes, os passos de resolução e ligações para a documentação relacionada.

Pode fazer duplo clique num alerta ativo ou resolvido para abrir um painel novo com informações adicionais, passos que pode tomar para resolver o alerta e ligações para documentação relevante. Pode também visualizar dados históricos de alertas que foram resolvidos no passado.

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Análise de Utilização do AD FS
A Análise de Utilização do Azure AD Connect Health analisa o tráfego de autenticação dos servidores de federação. Pode fazer duplo clique na caixa de análise de utilização para abrir o painel de análise de utilização, que lhe mostra várias métricas e agrupamentos.

> [!NOTE]
> Para utilizar a Análise de Utilização com o AD FS, tem de se certificar de que a auditoria do AD FS está ativada. Para obter mais informações, consulte [Ativar Auditoria do AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/report1.png)

Para selecionar métricas adicionais, especifique um intervalo de tempo ou, para alterar o agrupamento, clique com o botão direito do rato no gráfico de análise de utilização e selecione Editar Gráfico. Em seguida, pode especificar o intervalo de tempo, selecionar outra métrica e alterar o agrupamento. Pode ver a distribuição do tráfego de autenticação com base em diferentes “métricas” e agrupar cada uma com os parâmetros “agrupar por” relevantes descritos na secção seguinte:

**Métrica: Total de pedidos** - Total o número de pedidos processados por servidores do AD FS.

|Agrupar por | O que significa agrupamento e por que motivo é útil? |
| --- | --- |
| Todos | Apresenta a contagem do número total de pedidos processados por todos os servidores do AD FS.|
| Aplicação | Agrupa o total de pedidos com base na entidade confiadora visada. Este agrupamento é útil para entender que aplicação está a receber que percentagem do tráfego total. |
|  Servidor |Agrupa o total de pedidos com base no servidor que os processou. Este agrupamento é útil para entender a distribuição de carga do tráfego total.
| Associação à área de trabalho |Agrupa o total de pedidos consoante sejam provenientes de dispositivos que estão associados à área de trabalho (conhecidos). Este agrupamento é útil para entender se os recursos são acedidos através de dispositivos que são desconhecidos da infraestrutura de identidade. |
|  Método de Autenticação | Agrupa o total de pedidos com base no método de autenticação utilizado para a autenticação. Este agrupamento é útil para entender o método de autenticação comum que é utilizado para autenticação. Seguem-se os métodos de autenticação possíveis <ol> <li>Autenticação Integrada do Windows (Windows)</li> <li>Autenticação Baseada em Formulários (Formulários)</li> <li>SSO (Início de Sessão Único)</li> <li>Autenticação de Certificados X509 (Certificado)</li> <br>Se os servidores de federação receberem o pedido com um Cookie de SSO, esse pedido é contabilizado como SSO (Início de Sessão Único). Nestes casos, se o cookie for válido, o utilizador não é solicitado a fornecer credenciais e obtém acesso totalmente integrado à aplicação. Este comportamento é comum se tiver várias entidades confiadoras protegidas pelos servidores de federação. |
| Localização na Rede | Agrupa o total de pedidos com base na localização na rede do utilizador. Pode ser intranet ou extranet. Este agrupamento é útil para saber que percentagem do tráfego provêm da intranet versus a extranet. |


**Métrica: Total de pedidos falhados** -o número total de pedidos falhados processados pelo serviço de Federação. (Esta métrica só está disponível no AD FS para o Windows Server 2012 R2)

|Agrupar por | O que significa agrupamento e por que motivo é útil? |
| --- | --- |
| Tipo de Erro | Mostra o número de erros com base em tipos de erros predefinidos. Este agrupamento é útil para entender os tipos comuns de erros. <ul><li>Nome de utilizador incorreta ou palavra-passe: Erros devido a nome de utilizador incorreta ou a palavra-passe.</li> <li>"Bloqueio de extranet": Falhas devido a pedidos recebida de um utilizador que foi bloqueado da extranet </li><li> "Palavra-passe expirada": Falhas devido a utilizadores iniciar sessão com uma palavra-passe expirada.</li><li>"Conta desativada": Falhas devido a utilizadores iniciar sessão com uma conta desabilitada.</li><li>"Autenticação do dispositivo": Falhas devido a utilizadores que não se autenticam utilizando a autenticação do dispositivo.</li><li>"Autenticação de certificados de utilizador": Falhas devido a utilizadores que não se autenticam devido a um certificado inválido.</li><li>"MFA": Falhas devido utilizadores que não se autenticam utilizando o multi-factor Authentication.</li><li>"Outras credenciais": "Autorização de emissão": Falhas devido a falhas de autorização.</li><li>"Delegação de emissão": Falhas devido a erros de delegação de emissão.</li><li>"Aceitação de token": Falhas devido ao ADFS rejeitar o token de um fornecedor de identidade de terceiros.</li><li>"Protocol": Falha devido a erros de protocolo.</li><li>"Desconhecido": Captura todas as. Todas as outras falhas que não se encaixem nas categorias definidas.</li> |
| Servidor | Agrupa os erros com base no servidor. Este agrupamento é útil para entender a distribuição de erros pelos servidores. Uma distribuição desigual pode indicar um servidor num estado defeituoso. |
| Localização na Rede | Agrupa os erros com base na localização na rede dos pedidos (intranet vs. extranet). Este agrupamento é útil para entender o tipo de pedidos que estão a falhar. |
|  Aplicação | Agrupa as falhas com base na aplicação de destino (entidade confiadora). Este agrupamento é útil para compreender que aplicação de destino está a ter o maior número de erros. |

**Métrica: O número de utilizadores** – média número de utilizadores exclusivos em autenticação ativa com o AD FS

|Agrupar por | O que significa agrupamento e por que motivo é útil? |
| --- | --- |
|Todos |Esta métrica disponibiliza uma contagem do número médio de utilizadores que utilizam o serviço de federação no intervalo de tempo selecionado. Os utilizadores não são agrupados. <br>A média depende do intervalo de tempo selecionado. |
| Aplicação |Agrupa o número médio de utilizadores com base na aplicação de destino (entidade confiadora). Este agrupamento é útil para entender quantos utilizadores estão a utilizar que aplicação. |

## <a name="performance-monitoring-for-ad-fs"></a>Monitorização de Desempenho do AD FS
A Monitorização de Desempenho do Azure AD Connect Health fornece informações de monitorização sobre métricas. Ao selecionar a caixa Monitorização, é apresentado um painel novo com informações detalhadas sobre as métricas.

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/perf1.png)

Ao selecionar a opção Filtrar no topo do painel, pode filtrar por servidor para ver as métricas de um servidor específico. Para alterar as métricas, clique com o botão direito do rato no gráfico de monitorização no painel de monitorização e selecione Editar Gráfico (ou selecione o botão Editar Gráfico). No novo painel apresentado, pode selecionar métricas adicionais na lista pendente e especificar um intervalo de tempo para ver os dados de desempenho.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>Principais 50 Utilizadores com inícios de sessão falhados por Nome de Utilizador/Palavra-passe
Uma das razões comuns para um pedido de autenticação falhado num servidor do AD FS é um pedido com credenciais inválidas, ou seja, um nome de utilizador ou palavra-passe incorreto. Normalmente, acontece aos utilizadores devido a palavras-passe complexas, palavras-passe esquecidas ou erros ortográficos.

No entanto, existem outros motivos que podem resultar num número inesperado de pedidos a serem processados pelos servidores do AD FS, tais como: Um aplicativo que caches de credenciais de utilizador e estas expiram ou um utilizador mal intencionado a tentar iniciar sessão numa conta com uma série de palavras-passe bem conhecidas. Estes dois exemplos são motivos válidos que podem levar a um aumento nos pedidos.

O Azure AD Connect Health para ADFS fornece um relatório sobre os principais 50 utilizadores com tentativas de início de sessão falhadas devido a palavra-passe ou nome de utilizador inválido. Este relatório é obtido através do processamento dos eventos de auditoria gerados por todos os servidores AD FS nos farms.

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/report1a.png)

Neste relatório, terá acesso fácil às seguintes informações:

* N.º total de pedidos falhados com nome de utilizador/palavra-passe incorretos nos últimos 30 dias.
* N.º médio de utilizadores que falharam com um início de sessão com nome de utilizador/palavra-passe incorretos, por dia.

Clicar nesta parte leva-o para o painel do relatório principal, que disponibiliza detalhes adicionais. Este painel inclui um gráfico com informações de tendências para ajudar a estabelecer uma linha de base sobre pedidos com nome de utilizador ou palavra-passe errados. Além disso, fornece a lista dos 50 principais utilizadores com mais tentativas falhadas durante a última semana. Tenha em conta que os 50 principais utilizadores da última semana podem ajudar a identificar picos de palavras-passe erradas.  

O gráfico fornece as seguintes informações:

* O n.º total de inícios de sessão falhados devido a um nome de utilizador/palavra-passe incorreto numa base diária.
* O n.º total de utilizadores exclusivos que falharam inícios de sessão numa base diária.
* Endereço IP do cliente do último pedido

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/report3a.png)

O relatório disponibiliza as seguintes informações:

| Item de Relatório | Descrição |
| --- | --- |
| ID de Utilizador |Mostra o ID de utilizador que foi utilizado. Este valor é o que o utilizador escreveu, que, em alguns casos, é o ID errado que está a ser utilizado. |
| Tentativas Falhadas |Mostra o n.º total de tentativas falhadas para esse ID de utilizador específico. A tabela está ordenada por ordem descendente do maior número de tentativas falhadas. |
| Última Falha |Mostra o carimbo de data/hora em que ocorreu a última falha. |
| IP da Última Falha |Mostra o endereço IP do Cliente no pedido incorreto mais recente. Se vir mais do que um endereços IP neste valor, pode incluir um IP de cliente para reencaminhar juntamente com a última tentativa de pedido IP do utilizador.  |

> [!NOTE]
> Este relatório é atualizado automaticamente a cada 12 horas com novas informações recolhidas nesse período de tempo. Sendo assim, as tentativas de início de sessão nas últimas 12 horas poderão não estar incluídas no relatório.

## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Relatório de IP arriscado ](how-to-connect-health-adfs-risky-ip.md)

