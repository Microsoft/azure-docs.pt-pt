---
title: Gerar relatórios
description: Obtenha informações sobre a atividade da rede, riscos, ataques e tendências utilizando ferramentas de reporte do Defender para ioT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842565"
---
# <a name="generate-reports"></a>Gerar relatórios

Obtenha informações sobre a atividade da rede, riscos, ataques e tendências utilizando ferramentas de reporte do Azure Defender para as ferramentas de reporte de IoT. As ferramentas estão disponíveis para gerar relatórios:

- Baseado na atividade detetada por sensores individuais.
- Baseado na atividade detetada por todos os sensores. 

Estes relatórios são gerados a partir da consola de gestão no local.

## <a name="reports-for-sensor-risk-assessment"></a>Relatórios para avaliação do risco de sensores

O relatório de avaliação de risco permite-lhe gerar uma pontuação de segurança para cada dispositivo de rede, bem como uma pontuação de segurança global da rede. A pontuação global representa a percentagem de 100% de segurança.

Esta pontuação baseia-se em resultados da inspeção de pacotes, motores de modelação comportamental e um design de máquina estatal específico da SCADA. Uma vasta gama de outras informações está disponível, tais como:

- Problemas de configuração

- Vulnerabilidade do dispositivo priorizada por nível de segurança

- Problemas de segurança da rede

- Questões operacionais da rede

- Vetores de ataque

- Ligações às redes ics

- Conexões à Internet

- Indicadores industriais de malware

- Problemas de protocolo

  - Dispositivos Seguros: Dispositivos com uma pontuação de segurança superior a 90%.

- **Dispositivos Seguros**: Dispositivos com uma pontuação de segurança superior a 90%.

- **Dispositivos vulneráveis**: Dispositivos com uma pontuação de segurança inferior a 70%.

- **Dispositivos que precisam de ser melhorados**: Dispositivos com uma pontuação de segurança entre 70% e 89%.

Para criar um relatório:

1. Selecione **Avaliação de Risco** no menu lateral.
2. Selecione um sensor na lista de drop-down **do sensor Select.**
3. **Selecione Relatório de Geração**.
4. Selecione **Baixar** na secção Relatórios Arquivados.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Uma visão da avaliação de risco.":::

Para importar um logotipo da empresa:

Para importar um logotipo da empresa:

- Selecione **o logotipo de importação**.

## <a name="reports-for-sensor-attack-vector"></a>Relatórios para o vetor de ataque de sensores

Os relatórios de vetores de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis. Estas vulnerabilidades podem dar a um intruso acesso a dispositivos de rede chave. O Simulador de Vetores de Ataque calcula vetores de ataque em tempo real e analisa todos os vetores de ataque para um alvo específico.

Trabalhar com o vetor de ataque permite-lhe avaliar o efeito das atividades de mitigação na sequência de ataque. Pode então determinar, por exemplo, se uma atualização do sistema perturba o caminho do atacante, quebrando a cadeia de ataque, ou se permanece um caminho de ataque alternativo. Esta informação ajuda-o a priorizar atividades de reparação e mitigação.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Veja os seus alertas no centro de controlo.":::

> [!NOTE]
> Administradores e analistas de segurança podem executar os procedimentos descritos nesta secção.

Para criar uma simulação de vetor de ataque:

1. Selecione :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="mais sinal":::no menu lateral para adicionar uma Simulação.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="A simulação do vetor de ataque.":::

2. Introduza propriedades de simulação:

   - **Nome**: Nome da simulação.

   - **Vetores máximos**: O número máximo de vetores numa única simulação.

   - **Mostrar no mapa do dispositivo**: Mostre o vetor de ataque como um filtro no mapa do dispositivo.

   - **Todos os dispositivos Source**: O vetor de ataque considerará todos os dispositivos como uma fonte de ataque.

   - **Fonte de ataque**: O vetor de ataque considerará apenas os dispositivos especificados como uma fonte de ataque.

   - **Todos os dispositivos target**: O vetor de ataque considerará todos os dispositivos como alvo de ataque.

   - **Alvo de ataque**: O vetor de ataque considerará apenas os dispositivos especificados como alvo de ataque.

   - **Excluir dispositivos**: Os dispositivos especificados serão excluídos da simulação do vetor de ataque.

   - **Excluir sub-redes**: As sub-redes especificadas serão excluídas da simulação do vetor de ataque.

3. Selecione **Adicionar Simulação**. A simulação será adicionada à lista de simulações.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Adicione uma nova simulação.":::

4. Selecione :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: se pretender editar a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: se pretender eliminar a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: se quiser marcar a simulação como uma das favoritas.

5. Uma lista de vetores de ataque aparece e inclui pontuação vetorial (em 100), dispositivo de origem de ataque e dispositivo alvo de ataque. Selecione um ataque específico para representação gráfica de vetores de ataque.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vetores de ataque.":::

## <a name="sensor-data-mining-queries"></a>Consultas de mineração de dados de sensores

As ferramentas de mineração de dados permitem-lhe gerar informações abrangentes e granulares sobre os seus dispositivos de rede em várias camadas. Por exemplo, pode criar uma consulta baseada em:

- Períodos de tempo

- Ligações à internet

- Portas

- Protocolos

- Versões de firmware

- Comandos de programação

- Inatividade do dispositivo

Pode afinar o relatório com base em filtros. Por exemplo, pode consultar uma sub-rede específica na qual o firmware foi atualizado.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Lista de dispositivos ativos.":::

Várias ferramentas estão disponíveis para gerir consultas. Por exemplo, pode exportar e economizar.

> [!NOTE]
> Administradores e analistas de segurança têm acesso a opções de mineração de dados.

### <a name="dynamic-updates"></a>Atualizações dinâmicas

As consultas de mineração de dados que cria são atualizadas dinamicamente cada vez que as abre. Por exemplo:

- Se criar um relatório para versões de firmware em dispositivos no dia 1 de junho e abrir novamente o relatório no dia 10 de junho, este relatório será atualizado com informações precisas para o dia 10 de junho.

- Se criar um relatório para detetar novos dispositivos descobertos nos últimos 30 dias a 1 de junho e abrir o relatório no dia 30 de junho, os resultados serão apresentados nos últimos 30 dias.

### <a name="data-mining-use-cases"></a>Casos de utilização de dados-mineração

Você pode usar consultas para lidar com uma vasta gama de necessidades de segurança para várias equipas de segurança:

- **Resposta a incidentes SOC**: Gere um relatório em tempo real para ajudar a lidar com a resposta imediata ao incidente. Por exemplo, gere um relatório para uma lista de dispositivos que podem necessitar de remendos.

- **Forense :** Gerar um relatório baseado em dados históricos para relatórios de investigação.

- **Integridade da rede de TI**: Gere um relatório que ajude a melhorar a segurança geral da rede. Por exemplo, gere um relatório que lista dispositivos com credenciais de autenticação fracas.

- **Visibilidade**: Gere um relatório que cubra todos os itens de consulta para visualizar todos os parâmetros de base da sua rede.

### <a name="data-mining-storage"></a>Armazenamento de mineração de dados

As informações de extração de dados são guardadas e armazenadas continuamente, exceto quando um dispositivo é eliminado. Os resultados da mineração de dados podem ser exportados e armazenados externamente para um servidor seguro. Além disso, o sensor realiza backups diários automáticos para garantir a continuidade e preservação dos dados do sistema.

### <a name="data-mining-and-reports"></a>Mineração de dados e relatórios 

Relatórios regulares, acedidos a partir da opção **Relatórios,** são relatórios pré-definídos de data-mining. Não são consultas dinâmicas como estão disponíveis na mineração de dados, mas uma representação estática dos resultados da consulta de data-mineração.

Os resultados da consulta de data-mineração não estão disponíveis para os utilizadores de RO. Os administradores e analistas de segurança que querem que os utilizadores de RO tenham acesso à informação gerada por consultas de mineração de dados devem guardar a informação como relatório.

### <a name="predefined-queries"></a>Consultas predefinidas

Estão disponíveis as seguintes consultas predefinidas. Estas consultas são geradas em tempo real.

- **CVEs**: Uma lista de dispositivos detetados com vulnerabilidades conhecidas nas últimas 24 horas.

- **CV Excluídos**: Uma lista de todos os CVEs que foram excluídos manualmente. Para obter resultados mais precisos em relatórios va e vetores de ataque, você pode personalizar a lista CVE manualmente, incluindo e excluindo CVEs.

- **Atividade da Internet**: Dispositivos ligados à internet.

- **Dispositivos nãoactivos**: Dispositivos que não comunicam nos últimos sete dias.

- **Dispositivos ativos**: Dispositivos de rede ativos nas últimas 24 horas.

- **Acesso remoto**: Dispositivos que comunicam através de protocolos de sessão remota.

- **Comandos de programação**: Dispositivos que enviam programação industrial.

Estes relatórios são automaticamente acessíveis a partir do ecrã **Relatórios,** onde os utilizadores de RO e outros utilizadores podem vê-los. Os utilizadores de RO não conseguem aceder a relatórios de mineração de dados.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="O ecrã de mineração de dados.":::

### <a name="create-a-data-mining-report"></a>Criar um relatório de mineração de dados

Para criar um relatório de mineração de dados:

1. Selecione **Data Mining** a partir do menu lateral. Relatórios sugeridos predefinidos aparecem automaticamente.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Selecione a mineração de dados a partir do painel lateral.":::

2. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Selecione **Novo Relatório** e defina o relatório.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Crie um novo relatório preenchendo este ecrã.":::

   Estão disponíveis os seguintes parâmetros:

   - Forneça um nome e descrição do relatório.

   - Para categorias, selecione:

      - **Categorias (Todas)** para visualizar todos os resultados do relatório sobre todos os dispositivos da sua rede.

      - **Genérico** para escolher categorias padrão.

   - Selecione parâmetros específicos de interesse para si.

   - Escolha uma ordem de **classificação (Encomenda por).** Ordenar resultados com base na atividade ou na categoria.

   - **Selecione Guardar para reportar páginas** para guardar os resultados do relatório como um relatório acessível a partir da página **Relatório.** Isto permitirá que os utilizadores de RO executem o relatório que criou.

   - Selecione **Filtros (Adicionar)** para adicionar mais filtros. Os pedidos wildcard são suportados.

   - Especifique um grupo de dispositivos (definido no mapa do dispositivo).

   - Especifique um endereço IP.

   - Especifique uma porta.

   - Especifique um endereço MAC.

4. Selecione **Guardar**. Relatório de resultados abertos na página **Data Mining.**

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Reportar os resultados como visto na página data mining.":::

### <a name="manage-data-mining-reports"></a>Gerir relatórios de extração de dados

O quadro que se segue descreve opções de gestão para a extração de dados:

| Imagem de ícone | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Edite os parâmetros do relatório. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportar como PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportar como CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Mostre informações adicionais, como a data modificada pela última vez. Utilize esta função para criar uma imagem de resultado de consulta. Você pode precisar fazer isso para uma investigação mais aprofundada com líderes de equipa ou analistas da SOC, por exemplo. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Exiba na página **Relatórios** ou esconda-se na página **Relatórios.** :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Esconda ou revele os seus relatórios."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Elimine o relatório. |

#### <a name="create-customized-directories"></a>Criar diretórios personalizados 

Pode organizar a informação extensiva para consultas de mineração de dados, criando diretórios para categorias. Por exemplo, pode criar diretórios para protocolos ou locais.

Para criar um novo diretório:

1. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: para adicionar um novo diretório.

2. Selecione **Novo Diretório** para exibir o novo formulário de diretório.

3. Diga o nome do novo diretório.

4. Arraste os relatórios necessários para o novo diretório. A qualquer momento, pode arrastar o relatório de volta para a vista principal.

5. Clique com o botão direito no novo diretório para abrir, editar ou apagar.

#### <a name="create-snapshots-of-report-results"></a>Criar instantâneos dos resultados do relatório

Talvez precise de guardar certos resultados de consultas para uma investigação mais aprofundada. Por exemplo, pode precisar de partilhar resultados com várias equipas de segurança.

As imagens são guardadas nos resultados do relatório e não geram consultas dinâmicas.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Fotografias.":::

Para criar um instantâneo:

1. Abra o relatório necessário.

2. Selecione o ícone de informação :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. **Selecione Take New**.

4. Introduza um nome para o instantâneo e **selecione Guardar**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Tira uma foto.":::

#### <a name="customize-the-cve-list"></a>Personalize a lista CVE

Pode personalizar manualmente a lista CVE da seguinte forma:

  - Incluir/excluir CVEs

  - Alterar a pontuação do CVE

Para efetuar alterações manuais no relatório CVE:

1.  A partir do menu lateral, selecione **Data Mining**.

2. Selecione :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: no canto superior esquerdo da janela Data **Mining.** Em seguida, selecione **New Report**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Criar um novo relatório.":::

3. A partir do painel esquerdo, selecione uma das seguintes opções:

   - **Vulnerabilidades Conhecidas**: Seleciona ambas as opções e apresenta resultados nas duas tabelas do relatório, uma com CVEs e outra com CVEs excluídas.

   - **CVEs**: Selecione esta opção para apresentar uma lista de todos os CVEs.

   - **CVEs excluídos**: Selecione esta opção para apresentar uma lista de todos os CVEs excluídos.

4. Preencha as informações **de Nome** e **Descrição** e selecione **Guardar**. O novo relatório aparece na janela **data mining.**

5. Para excluir os CVEs, abra o relatório de extração de dados para os CVEs. A lista de todos os CVEs aparece.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Relatório C V E.":::

6. Para ativar a seleção de itens na lista, :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: selecione e selecione os CVEs que pretende personalizar. A barra **de operações** aparece na parte inferior.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Screenshot da barra de operações de mineração de dados.":::

7. Selecione os CVEs que pretende excluir e, em seguida, **selecione Delete Records**. Os CVEs que selecionou não aparecem na lista de CVEs e aparecerão na lista de CVEs excluídos quando gerar um.

8. Para incluir os CVEs excluídos na lista de CVEs, gere o relatório para CVEs excluídos e elimine dessa lista os itens que pretende incluir novamente na lista de CVEs.

9. Selecione os CVEs nos quais pretende alterar a pontuação e, em seguida, selecione **Update CVE Score**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Atualize a pontuação do CVE.":::

10. Introduza a nova pontuação e selecione **OK**. A pontuação atualizada aparece nos CVEs que selecionou.

### <a name="reports-based-on-data-mining"></a>Relatórios baseados na mineração de dados

Os relatórios refletem a informação gerada pelos resultados da consulta de dados. Isto inclui relatórios de mineração de dados predefinidos, que estão disponíveis na visão relatórios. Os analistas de administrador e de segurança também podem gerar consultas de mineração de dados personalizadas e guardá-las como relatórios. Estes relatórios também estão disponíveis para utilizadores de RO.

Para gerar um relatório:

1. Selecione **Relatórios** no menu lateral.

2. Escolha o relatório necessário para visualizar. A escolha pode ser **relatórios personalizados** ou **gerados por automáticos,** tais como **Comandos de Programação** e **Acesso Remoto.**

3. Pode exportar o relatório selecionando um dos ícones no canto superior direito do ecrã:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportar para um ficheiro PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportar para um ficheiro CSV.

> [!NOTE] 
> O utilizador ro só pode ver relatórios criados para eles.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Selecione o relatório para gerar.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Relatório de acesso remoto gerado.":::

## <a name="reports-for-sensor-trends-and-statistics"></a>Relatórios sobre tendências e estatísticas de sensores

Você pode criar gráficos de widget e gráficos de tortas para obter informações sobre as tendências e estatísticas da rede. Os widgets podem ser agrupados em dashboards definidos pelo utilizador.

> [!NOTE]
> Apenas administradores e analistas de segurança podem executar os procedimentos nesta secção.

Para ver dashboards e widgets, selecione **Trends & Statistics** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot de uma investigação.":::

O painel é composto por widgets que descrevem graficamente os seguintes tipos de informação:

- Tráfego por porto
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por SISTEMA
  - Dispositivos desligados
- Conectividade gota por horas
- Alertas para incidentes por tipo
- Acesso à tabela de bases de dados
- Widgets de dissecação de protocolo
- Endereço Ethernet e IP:
  - Tráfego de endereços Ethernet e IP pelo serviço CIP
  - Tráfego de endereços Ethernet e IP por classe CIP
  - Tráfego de endereços Ethernet e IP por comando
- OPC:
  - Operações de gestão de topo da OPC
  - Operações de E/S de topo da OPC
- Siemens S7:
  - Tráfego S7 por função de controlo
  - Tráfego S7 por subfunção
- SRTP:
  - Tráfego SRTP por código de serviço
  - Erros SRTP de dia
- SuiteLink:
  - Etiquetas de topo suiteLink
  - Comportamento da etiqueta numérica SuiteLink
- Tráfego IEC-60870 pela ASDU
- Tráfego DNP3 por função
- Tráfego MMS por serviço
- Modbus tráfego por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  O tempo nos widgets é definido de acordo com o tempo do sensor.

## <a name="reports-for-the-on-premises-management-console"></a>Relatórios para a consola de gestão no local

A consola de gestão no local permite-lhe gerar relatórios para cada sensor que está ligado a ele. Os relatórios baseiam-se em consultas de mineração de dados de sensores que são realizadas.

Pode gerar os seguintes relatórios:

- **Dispositivos Ativos (Últimas 24 Horas)**: Apresenta uma lista de dispositivos que mostram a atividade da rede num período de 24 horas.

- **Dispositivos Não Ativos (Últimos 7 Dias)**: Apresenta uma lista de dispositivos que não mostram atividade de rede nos últimos sete dias.

- **Comandos de Programação**: Apresenta uma lista de dispositivos que enviaram comandos de programação nas últimas 24 horas.

- **Acesso Remoto**: Apresenta uma lista de dispositivos a que as fontes remotas acederam nas últimas 24 horas.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Screenshot da vista dos relatórios.":::

Ao escolher o sensor da consola de gestão no local, todos os relatórios personalizados configurados nesse sensor aparecem na lista de relatórios. Para cada sensor, pode gerar um relatório predefinido ou um relatório personalizado configurado nesse sensor.

Para gerar um relatório:

1. No painel esquerdo, **selecione Relatórios**. A janela **do Relatório** aparece.

2. A partir da lista de retirada **dos sensores,** selecione o sensor para o qual pretende gerar o relatório.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Screenshot da vista dos sensores.":::

3. A partir da lista de drop-down direita, selecione o relatório que pretende gerar.

4. Para criar um PDF dos resultados do relatório, selecione :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>Relatórios de avaliação de risco para a consola de gestão no local

Gere um relatório de avaliação de risco para cada sensor que esteja ligado à sua consola de gestão no local. Este relatório fornece informações sobre cada uma das redes que os seus sensores estão a monitorizar.

Os relatórios de avaliação de risco permitem-lhe gerar uma pontuação de segurança para cada dispositivo de rede, bem como uma pontuação global de segurança da rede. A pontuação geral baseia-se na inspeção profunda de pacotes, motores de modelação comportamental e um design de máquina estatal específico da SCADA. Uma vasta gama de outras informações está disponível. Por exemplo:

- Problemas de configuração

- Vulnerabilidade do dispositivo priorizada por nível de segurança

- Problemas de segurança da rede

- Questões operacionais da rede

- Vetores de ataque

- Ligações às redes ics

- Conexões à Internet

- Indicadores industriais de malware

- Problemas de protocolo

O relatório fornece recomendações de mitigação que o ajudarão a melhorar a sua pontuação de segurança.

- Dispositivos seguros: Dispositivos com uma pontuação de segurança superior a 90%.

- **Dispositivos vulneráveis**: Dispositivos com uma pontuação de segurança inferior a 70%.

- **Dispositivos que precisam de ser melhorados**: Dispositivos com uma pontuação de segurança entre 70% e 89%.

Para criar um relatório:

1. Selecione **Avaliação de Risco** no menu lateral.

2. Selecione um sensor na lista de retirada do **sensor Select.**

3. **Selecione Relatório de Geração**.

4. Selecione **Baixar** na secção **Relatórios Arquivados.**

Para importar um logotipo da empresa:

- Selecione **o logotipo de importação**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importe o seu logotipo através da vista de avaliação de risco.":::

## <a name="see-also"></a>Veja também
[Trabalhar com vistas do mapa do site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
