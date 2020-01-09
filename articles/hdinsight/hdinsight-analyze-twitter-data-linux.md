---
title: Analisar dados do Twitter com o Apache Hive-HDInsight do Azure
description: Saiba como usar Apache Hive e Apache Hadoop no HDInsight para transformar dados brutos do TWitter em uma tabela Hive pesquisável.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435607"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analisar dados do Twitter usando Apache Hive e Apache Hadoop no HDInsight

Saiba como usar [Apache Hive](https://hive.apache.org/) para processar dados do Twitter. O resultado é uma lista de usuários do Twitter que enviaram a maioria dos Tweets que contêm uma determinada palavra.

> [!IMPORTANT]  
> As etapas neste documento foram testadas no HDInsight 3,6.

## <a name="get-the-data"></a>Obter os dados

O Twitter permite que você recupere os dados para cada tweet como um documento JavaScript Object Notation (JSON) por meio de uma API REST. O [OAuth](https://oauth.net) é necessário para autenticação na API.

### <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter

1. Em um navegador da Web, entre no [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/). Selecione o link **inscrever-se agora** se você não tiver uma conta do Twitter.

2. Selecione **criar novo aplicativo**.

3. Insira o **nome**, a **Descrição**e o **site**. Você pode criar uma URL para o campo **site** . A tabela a seguir mostra alguns exemplos de valores a serem usados:

   | Campo | Valor |
   |--- |--- |
   | Nome |MyHDInsightApp |
   | Descrição |MyHDInsightApp |
   | Site |`https://www.myhdinsightapp.com` |

4. Selecione **Sim, concordo**e, em seguida, selecione **criar seu aplicativo do Twitter**.

5. Selecione a guia **permissões** . A permissão padrão é **somente leitura**.

6. Selecione a guia **chaves e tokens de acesso** .

7. Selecione **criar meu token de acesso**.

8. Selecione **testar OAuth** no canto superior direito da página.

9. Anote a **chave do consumidor**, o **segredo do consumidor**, o token de **acesso**e o segredo do **token de acesso**.

### <a name="download-tweets"></a>Baixar tweets

O código Python a seguir baixa o 10.000 tweets do Twitter e os salva em um arquivo chamado **tweets. txt**.

> [!NOTE]  
> As etapas a seguir são executadas no cluster HDInsight, já que o Python já está instalado.

1. Use o [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use os comandos a seguir para instalar o [tweepy](https://www.tweepy.org/), a [barra de progresso](https://pypi.python.org/pypi/progressbar/2.2)e outros pacotes necessários:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Use o seguinte comando para criar um arquivo chamado **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

1. Edite o código a seguir, substituindo `Your consumer secret`, `Your consumer key`, `Your access token`e `Your access token secret` pelas informações relevantes do seu aplicativo do Twitter. Em seguida, Cole o código editado como o conteúdo do arquivo **gettweets.py** .

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Ajuste o filtro de tópicos na última linha para controlar palavras-chave populares. O uso de palavras-chave populares no momento em que você executa o script permite uma captura mais rápida dos dados.

1. Use **Ctrl + X**e **Y** para salvar o arquivo.

1. Use o seguinte comando para executar o arquivo e baixar tweets:

    ```bash
    python gettweets.py
    ```

    Um indicador de progresso é exibido. Ele conta até 100% à medida que os tweets são baixados.

   > [!NOTE]  
   > Se estiver demorando muito tempo para que a barra de progresso avance, você deverá alterar o filtro para acompanhar os tópicos de tendência. Quando há muitos tweets sobre o tópico em seu filtro, você pode rapidamente obter os tweets 100 necessários.

### <a name="upload-the-data"></a>Carregar os dados

Para carregar os dados no armazenamento do HDInsight, use os seguintes comandos:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Esses comandos armazenam os dados em um local que todos os nós no cluster podem acessar.

## <a name="run-the-hiveql-job"></a>Executar o trabalho do HiveQL

1. Use o comando a seguir para criar um arquivo que contém instruções [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) :

   ```bash
   nano twitter.hql
   ```

    Use o seguinte texto como o conteúdo do arquivo:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Pressione **Ctrl + X**e, em seguida, pressione **Y** para salvar o arquivo.

1. Use o seguinte comando para executar o HiveQL contido no arquivo:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Esse comando executa o arquivo **Twitter. HQL** . Quando a consulta for concluída, você verá um prompt de `jdbc:hive2//localhost:10001/>`.

1. No prompt do beeline, use a seguinte consulta para verificar se os dados foram importados:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Essa consulta retorna um máximo de 10 Tweets que contêm a palavra **Azure** no texto da mensagem.

    > [!NOTE]  
    > Se você alterou o filtro no script `gettweets.py`, substitua o **Azure** por um dos filtros que você usou.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu como transformar um conjunto de dados JSON não estruturado em uma tabela [Apache Hive](https://hive.apache.org/) estruturada. Para saber mais sobre o hive no HDInsight, consulte os seguintes documentos:

* [Introdução ao HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analisar dados de atraso de vôo usando o HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
