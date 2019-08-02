---
title: Introdução-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Esta é uma visão geral mais profunda que destaca a Threat Modeling Tool em ação.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728204"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Introdução ao Threat Modeling Tool

O Microsoft Threat Modeling Tool 2018 foi lançado como GA em setembro de 2018 como um **[clique para baixar](https://aka.ms/threatmodelingtool)** gratuitamente. A alteração no mecanismo de entrega nos permite enviar por push as mais recentes melhorias e correções de bugs aos clientes sempre que abrirem a ferramenta, tornando-a mais fácil de manter e usar.
Este artigo orienta você pelo processo de introdução à abordagem de modelagem de ameaças do Microsoft SDL e mostra como usar a ferramenta para desenvolver ótimos modelos de ameaça como um backbone de seu processo de segurança.

Este artigo se baseia no conhecimento existente da abordagem de modelagem de ameaças do SDL. Para uma revisão rápida, consulte **[aplicativos Web de modelagem de risco](https://msdn.microsoft.com/library/ms978516.aspx)** e uma versão arquivada de **[descobrir falhas de segurança usando o artigo da abordagem STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** do MSDN publicado em 2006.

Para resumir rapidamente, a abordagem envolve a criação de um diagrama, a identificação de ameaças, a mitigação e a validação de cada mitigação. Este é um diagrama que realça esse processo:

![Processo do SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Iniciando o processo de modelagem de ameaças

Ao iniciar o Threat Modeling Tool, você observará algumas coisas, como mostrado na imagem:

![Página inicial em branco](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Seção de modelo de ameaça

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Botão comentários, sugestões e problemas** | O leva para o **[Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** sobre tudo o que é o SDL. Ele oferece a você uma oportunidade de ler o que os outros usuários estão fazendo, juntamente com soluções alternativas e recomendações. Se você ainda não consegue encontrar o que está procurando, envie tmtextsupport@microsoft.com um email para nossa equipe de suporte para ajudá-lo                                                                                                                            |
| **Criar um modelo**                          | Abre uma tela em branco para desenhar o diagrama. Certifique-se de selecionar qual modelo você gostaria de usar para seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Modelo para novos modelos**                 | Você deve selecionar qual modelo usar antes de criar um modelo. Nosso modelo principal é o modelo de modelo de risco do Azure, que contém estênceis, ameaças e atenuações específicas do Azure. Para modelos genéricos, selecione a base de dados de conhecimento do SDL TM no menu suspenso. Deseja criar seu próprio modelo ou enviar um novo para todos os usuários? Confira nossa página do GitHub do **[repositório de modelos](https://github.com/Microsoft/threat-modeling-templates)** para saber mais                              |
| **Abrir um modelo**                            | <p>Abre modelos de ameaças salvos anteriormente. O recurso de modelos abertos recentemente será ótimo se você precisar abrir seus arquivos mais recentes. Ao focalizar a seleção, você encontrará duas maneiras de abrir modelos:</p><p><ul><li>Abrir a partir deste computador – modo clássico de abrir um arquivo usando o armazenamento local</li><li>Abrir do OneDrive – as equipes podem usar pastas no OneDrive para salvar e compartilhar todos os seus modelos de ameaça em um único local para ajudar a aumentar a produtividade e a colaboração</li></ul></p> |
| **Guia de Introdução**                   | Abre a **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** página principal                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Seção de modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar novo modelo** | Abre um modelo em branco para você criar. A menos que você tenha amplo conhecimento na criação de modelos do zero, recomendamos que você crie a partir dos existentes |
| **Abrir modelo**       | Abre modelos existentes para fazer alterações                                                                                                              |

A equipe de Threat Modeling Tool está trabalhando constantemente para melhorar a experiência e a funcionalidade da ferramenta. Algumas pequenas alterações podem ocorrer no decorrer do ano, mas todas as principais alterações exigem regravações no guia. Consulte com frequência para garantir que você obtenha os comunicados mais recentes.

## <a name="building-a-model"></a>Criando um modelo

Nesta seção, seguimos:

- Cristina (um desenvolvedor)
- Ricardo (um gerente de programa) e
- Ashish (um testador)

Eles passam pelo processo de desenvolvimento de seu primeiro modelo de risco.

> Ricardo Olá Cristina, trabalhei no diagrama de modelo de risco e queria ter certeza de que temos os detalhes corretos. Você pode me ajudar a olhar isso?
> Cristina Com certeza. Vamos dar uma olhada.
> Ricardo abre a ferramenta e compartilha sua tela com Cristina.

![Modelo básico de ameaças](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina Ok, parece simples, mas você pode me orientá-lo?
> Ricardo Esqueça! Aqui está a divisão:
> - Nosso usuário humano é desenhado como uma entidade externa — um quadrado
> - Eles estão enviando comandos para o nosso servidor Web — o círculo
> - O servidor Web está consultando um banco de dados (duas linhas paralelas)

O que Ricardo acabou de mostrar Cristina é um DFD, curto para o **[diagrama de fluxo de dados](https://en.wikipedia.org/wiki/Data_flow_diagram)** . O Threat Modeling Tool permite que os usuários especifiquem limites de confiança, indicados pelas linhas pontilhadas vermelhas, para mostrar onde diferentes entidades estão no controle. Por exemplo, os administradores de ti exigem um sistema Active Directory para fins de autenticação, de modo que o Active Directory está fora de seu controle.

> Cristina Parece com o direito a mim. E quanto às ameaças?
> Ricardo Deixe-me mostrar.

## <a name="analyzing-threats"></a>Analisando ameaças

Quando ele clica na exibição de análise na seleção de menu de ícone (arquivo com lupa), ele é levado a uma lista de ameaças geradas que o Threat Modeling Tool encontrado com base no modelo padrão, que usa a abordagem do SDL chamada **[Stride (falsificação, violação, Divulgação de informações, repúdio, negação de serviço e elevação de privilégio)](https://en.wikipedia.org/wiki/STRIDE_(security))** . A ideia é que o software é fornecido sob um conjunto previsível de ameaças, que pode ser encontrado usando essas 6 categorias.

Essa abordagem é como proteger sua casa garantindo que cada porta e janela tenha um mecanismo de bloqueio em vigor antes de adicionar um sistema de alarme ou caçar-se após o ladrão.

![Ameaças básicas](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo começa selecionando o primeiro item na lista. Veja o que acontece:

Primeiro, a interação entre os dois estênceis é aprimorada

![Inter](./media/threat-modeling-tool-getting-started/interaction.png)

Segundo, informações adicionais sobre a ameaça aparecem no janela Propriedades de ameaças

![Informações de interação](./media/threat-modeling-tool-getting-started/interactioninfo.png)

A ameaça gerada ajuda a entender possíveis falhas de design. A categorização STRIDE dá a ele uma ideia sobre possíveis vetores de ataque, enquanto a descrição adicional informa exatamente o que está errado, juntamente com possíveis maneiras de atenuá-lo. Ele pode usar campos editáveis para escrever anotações nos detalhes da justificativa ou alterar as classificações de prioridade, dependendo da barra de bugs de sua organização.

Os modelos do Azure têm detalhes adicionais para ajudar os usuários a entenderem não apenas o que está errado, mas também como corrigi-lo adicionando descrições, exemplos e hiperlinks à documentação específica do Azure.

A descrição fez com que ele perceba a importância de adicionar um mecanismo de autenticação para impedir que os usuários sejam falsificados, revelando a primeira ameaça a ser realizada. Alguns minutos na discussão com Cristina, eles compreenderam a importância de implementar o controle de acesso e as funções. Ricardo preenchidos em algumas anotações rápidas para garantir que elas foram implementadas.

Como a Ricardo entrou nas ameaças de divulgação de informações, ele percebeu que o plano de controle de acesso exigia algumas contas somente leitura para auditoria e geração de relatórios. Ele perguntou se isso deve ser uma nova ameaça, mas as atenuações eram as mesmas, portanto, ele observou a ameaça adequadamente.
Ele também pensou na divulgação de informações um pouco mais e percebeu que as fitas de backup precisariam de criptografia, um trabalho para a equipe de operações.

As ameaças que não se aplicam ao design devido a atenuações ou garantias de segurança existentes podem ser alteradas para "não aplicável" na lista suspensa status. Há três outras opções: Não iniciado – seleção padrão, precisa de investigação – usada para acompanhar itens e atenuado – depois que ele é totalmente trabalhado.

## <a name="reports--sharing"></a>Relatórios & compartilhamento

Depois que o Ricardo passa pela lista com Cristina e adiciona observações importantes, mitigações/justificativas, prioridade e alterações de status, ele seleciona relatórios – > criar relatório completo – > Salvar relatório, que imprime um bom relatório para que ele passe com colegas para garantir que o trabalho de segurança adequado seja implementado.

![Informações de interação](./media/threat-modeling-tool-feature-overview/report.png)

Se Ricardo quiser compartilhar o arquivo, ele poderá fazer isso facilmente salvando na conta do OneDrive da sua organização. Depois de fazê-lo, ele pode copiar o link do documento e compartilhá-lo com seus colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões de modelagem de ameaças

Quando o Ricardo enviou seu modelo de ameaça para seu colega usando o OneDrive, o Ashish, o testador, foi desanimado. Parecia que o Ricardo e o Cristina perderam alguns casos de canto importantes, o que poderia ser facilmente comprometido. Seu ceticismo é um complemento aos modelos de risco.

Nesse cenário, depois que Ashish assumiu o modelo de ameaça, ele chamou para duas reuniões de modelagem de risco: uma reunião para sincronizar o processo e percorrer os diagramas e, em seguida, uma segunda reunião para revisão e aprovação de ameaças.

Na primeira reunião, Ashish gastou 10 minutos examinando todos por meio do processo de modelagem de ameaças do SDL. Em seguida, ele reuniu o diagrama de modelo de risco e começou a explicar em detalhes. Em cinco minutos, um componente ausente importante foi identificado.

Alguns minutos depois, Ashish e Ricardo entraram em uma discussão detalhada de como o servidor Web foi criado. Não era a maneira ideal de uma reunião continuar, mas todos nos concordaram que descobrir a discrepância no início economizaram tempo no futuro.

Na segunda reunião, a equipe abordou as ameaças, discutiu algumas maneiras de solucioná-las e se saiu do modelo de ameaça. Eles verificaram o documento no controle do código-fonte e continuaram com o desenvolvimento.

## <a name="thinking-about-assets"></a>Pensando sobre os ativos

Alguns leitores que têm a ameaça modelada podem notar que não falamos sobre ativos. Descobrimos que muitos engenheiros de software entendem melhor seus softwares do que compreendem o conceito de ativos e em quais ativos um invasor pode estar interessado.

Se você for o modelo de risco de uma casa, poderá começar pensando em sua família, fotos insubstituíveis ou trabalho valioso. Talvez você comece pensando em quem pode invadir e o sistema de segurança atual. Ou você pode começar considerando os recursos físicos, como o pool ou o piscina frontal. Eles são análogos a pensar sobre ativos, invasores ou design de software. Qualquer uma dessas três abordagens funciona.

A abordagem para a modelagem de ameaças que apresentamos aqui é consideravelmente mais simples do que a que a Microsoft fez no passado. Descobrimos que a abordagem de design de software funciona bem para muitas equipes. Esperamos que inclua seu.

## <a name="next-steps"></a>Próximos Passos

Envie suas dúvidas, comentários e preocupações com tmtextsupport@microsoft.como. **[Baixe](https://aka.ms/threatmodelingtool)** o Threat Modeling Tool para começar.
