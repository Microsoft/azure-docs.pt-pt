---
title: Data Factory Azure Copy Wizard
description: Saiba como utilizar o Data Factory Azure Copy Wizard para copiar dados de fontes de dados suportadas para afundar.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393382"
---
# <a name="azure-data-factory-copy-wizard"></a>Assistente de cópia da fábrica de dados Azure

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O Assistente de Cópia da Fábrica de Dados Azure facilita o processo de ingestão de dados, que normalmente é um primeiro passo num cenário de integração de dados de ponta a ponta. Ao passar pelo Azure Data Factory Copy Wizard, não precisa de compreender quaisquer definições JSON para serviços ligados, conjuntos de dados e oleodutos. O assistente cria automaticamente um pipeline para copiar dados da fonte de dados selecionada para o destino selecionado. Além disso, o Copy Wizard ajuda-o a validar os dados que estão a ser ingeridos no momento da autoria. Isto poupa tempo, especialmente quando está a ingerir dados pela primeira vez a partir da fonte de dados. Para iniciar o Copy Wizard, clique no azulejo **de dados copy** na página inicial da sua fábrica de dados.

![Assistente de Cópia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Projetado para big data
Este assistente permite-lhe mover facilmente dados de uma grande variedade de fontes para destinos em minutos. Depois de passar pelo assistente, é criado automaticamente um pipeline com uma atividade de cópia, juntamente com entidades dependentes da Data Factory (serviços e conjuntos de dados ligados). Não são necessários passos adicionais para criar o gasoduto.   

![Selecionar origem de dados](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Para obter instruções passo a passo para criar um pipeline de amostra para copiar dados de uma bolha Azure para uma tabela de base de dados Azure SQL, consulte o tutorial do [Copy Wizard](data-factory-copy-data-wizard-tutorial.md).

O assistente é projetado com grandes dados em mente desde o início, com suporte para diversos tipos de dados e objetos. Pode autor de pipelines data factory que movem centenas de pastas, ficheiros ou tabelas. O assistente suporta a pré-visualização automática de dados, captura de esquemas e mapeamento e filtragem de dados.

## <a name="automatic-data-preview"></a>Pré-visualização automática de dados
Pode pré-visualizar parte dos dados a partir da fonte de dados selecionada para validar se os dados são o que pretende copiar. Além disso, se os dados de origem estiverem num ficheiro de texto, o Copy Wizard analisa o ficheiro de texto para aprender automaticamente os delimiters e esquemas de linha e coluna.

![Definições do formato do ficheiro](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Captura e mapeamento de esquemas
O esquema dos dados de entrada pode não corresponder ao esquema dos dados de saída em alguns casos. Neste cenário, é necessário mapear colunas do esquema de origem para colunas a partir do esquema de destino.

> [!TIP]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL para a Azure Synapse Analytics, se a tabela não existir na loja de destino, a Data Factory suporta a criação de quadros automáticos utilizando o esquema da fonte. Saiba mais a partir da [Move datas de e para a Azure Synapse Analytics usando Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Utilize uma lista de recuos para selecionar uma coluna do esquema de origem para mapear para uma coluna no esquema de destino. O Copy Wizard tenta compreender o seu padrão de mapeamento de colunas. Aplica o mesmo padrão ao resto das colunas, de modo que não precisa de selecionar cada uma das colunas individualmente para completar o mapeamento do esquema. Se preferir, pode anular estes mapeamentos utilizando as listas de drop-down para mapear as colunas uma a uma. O padrão torna-se mais preciso à medida que mapeia mais colunas. O Copy Wizard atualiza constantemente o padrão e, em última análise, atinge o padrão certo para o mapeamento da coluna que pretende alcançar.     

![Mapeamento de Schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrar dados
Pode filtrar dados de origem para selecionar apenas os dados que precisam de ser copiados para a loja de dados da pia. A filtragem reduz o volume dos dados a copiar para a loja de dados da pia e, portanto, aumenta o rendimento da operação de cópia. Fornece uma forma flexível de filtrar dados numa base de dados relacional utilizando a linguagem de consulta SQL, ou ficheiros numa pasta de blob Azure utilizando [funções e variáveis data factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtragem de dados numa base de dados
A imagem que se segue mostra uma consulta SQL utilizando a `Text.Format` função e `WindowStart` a variável.

![Validar expressões](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtragem de dados numa pasta de bolhas Azure
Pode utilizar variáveis no caminho da pasta para copiar dados de uma pasta que é determinada em tempo de execução com base em [variáveis do sistema](data-factory-functions-variables.md#data-factory-system-variables). As variáveis suportadas são: **{year}**, **{month}** **{day}** **,{hour}**, **{minute}**, e **{custom}**. Por exemplo: inputfolder/{year}/{month}/{day}.

Suponha que tenha pastas de entrada no seguinte formato:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Clique no botão **procurar** **ficheiro ou pasta,** navegue por uma destas pastas (por exemplo, 2016->03->01->02) e clique em **Escolher**. Devia ver `2016/03/01/02` na caixa de texto. Agora, substitua **2016** por **{ano}**, **03** por **{mês}**, **01** por **{day}** e **02** por **{hora}**, e prima a tecla **Separador.** Deve ver listas de drop-down para selecionar o formato destas quatro variáveis:

![Usando variáveis do sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Como mostrado na imagem seguinte, também pode utilizar uma variável **personalizada** e quaisquer [cordas de formato suportados](/dotnet/standard/base-types/custom-date-and-time-format-strings). Para selecionar uma pasta com esta estrutura, utilize primeiro o botão **Procurar.** Em seguida, substitua um valor por **{custom}** e prima a tecla **'Separador'** para ver a caixa de texto onde pode escrever a cadeia de formato.     

![Usando variável personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opções de agendamento
Pode executar a operação de cópia uma vez ou num horário (de hora em hora, diariamente e assim por diante). Ambas as opções podem ser usadas para a amplitude dos conectores em ambientes, incluindo no local, nuvem e cópia de ambiente de trabalho local.

Uma operação de cópia única permite o movimento de dados de uma fonte para um destino apenas uma vez. Aplica-se a dados de qualquer tamanho e de qualquer formato suportado. A cópia agendada permite-lhe copiar dados sobre uma recorrência prescrita. Pode utilizar configurações ricas (como retrasar, cronometragem e alertas) para configurar a cópia agendada.

![Propriedades de agendamento](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção explora métodos comuns de resolução de problemas para copy wizard in Azure Data Factory.

> [!NOTE] 
> Estas dicas de resolução de problemas aplicam-se ao assistente de cópia na versão 1 da Data Factory. Para data factory v2, consulte o guia de resolução de problemas na [Troubleshoot Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Código de erro: Incapaz de validar no Copy Wizard

- **Sintomas**: No primeiro passo do Copy Wizard, encontra a mensagem de aviso de "Incapaz de Validar".
- **Causas**: Isto pode acontecer quando todos os cookies de terceiros estiverem desativados.
- **Resolução:** 
    - Utilize o internet Explorer ou o navegador Microsoft Edge.
    - Se estiver a utilizar o navegador Chrome, siga as instruções abaixo para adicionar a exceção cookies para *microsoftonline.com* e *windows.net*.
        1.  Abra o navegador Chrome.
        2.  Clique na chave inglesa ou três linhas à direita (Personalize e controle o Google Chrome).
        3.  Clique em **Definições**.
        4.  Pesse **cookies** ou vá para **a Privacidade** em Definições Avançadas.
        5.  Selecione **Definições de conteúdo**.    
        6.  Os cookies devem ser definidos para **permitir a fixação de dados locais (recomendado)**.
        7.  Clique **Em Gerir exceções**. Sob **o padrão de nome hospedeiro** introduza o seguinte, e **certifique-se de que Allow** é o conjunto de comportamento.
            - login.microsoftonline.com
            - login.windows.net
        8.  Feche o navegador e relançe.
    - Se estiver a utilizar o navegador Firefox, siga as instruções abaixo para adicionar a exceção cookies.
        1. A partir do menu Firefox, aceda às  >  **Opções de Ferramentas.**
        2. Em   >  **Histórico de Privacidade,** pode ver que a definição atual é **Utilizar configurações personalizadas para o histórico**.
        3. Em **Aceitar cookies de terceiros**, a sua configuração atual pode ser **Nunca**, então deve clicar em Exceções no direito de adicionar os **seguintes** sites.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Feche o navegador e relançe. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Código de erro: Não é possível abrir a página de login e introduzir senha

- **Sintomas**: Copy Wizard redireciona-o para a página de login, mas a página de login não aparece com sucesso.
- **Causas**: Este problema pode acontecer se alterar o ambiente de rede de escritórios para rede doméstica. Existem algumas caches nos navegadores. 
- **Resolução:** 
    1.  Feche o navegador e tente novamente. Vá para o próximo passo se a questão ainda existir.   
    2.  Se estiver a utilizar o navegador Internet Explorer, tente abri-lo em modo privado (Prima "Ctrl" + "Shift" + "P"). Se estiver a utilizar o navegador Chrome, tente abri-lo em modo incógnito (Prima "Ctrl" + "shift" + "N"). Vá para o próximo passo se a questão ainda existir. 
    3.  Tente usar outro navegador. 


## <a name="next-steps"></a>Passos seguintes
Para uma rápida passagem da utilização do Assistente de Cópia da Fábrica de Dados para criar um oleoduto com a Atividade de Cópia, consulte [Tutorial: Crie um oleoduto utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md).