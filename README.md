<h1> Desafio: DynamoDB</h1>

<h2>Descrição do Desafio</h2>
<p>
Características Relacionais (SQL) e Não Relacionais (NoSQL) usando o mesmo banco de dados? Isso é possível? Com o DynamoDB sim! Entenda um pouco das possibilidades desse banco de dados totalmente gerenciado da AWS. Para isso, nosso super expert apresenta uma série de boas práticas para o DynamoDB em um desafio totalmente prático. 
</p>

<h2>🛑 Pré-Requisitos</h2>

<p>
✅ Amazon DynamoDB<br>
✅ Amazon CLI para execução em linha de comando<br>
</p>

<h2> ‼️ Comandos para execução ‼️</h2>

<h3>🔻 Criar uma tabela:</h3> 

```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

<h3>🔻 Inserir um item:</h3> 
```
aws dynamodb put-item \
    --table-name Music \
    --item file://itemmusic.json \
```

<h3>🔻 Inserir múltiplos itens:</h3> 
```
aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
```

<h3>🔻 Criar um index global secundário baeado no título do álbum:</h3> 
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

<h3>🔻 Criar um index global secundário baseado no nome do artista e no título do álbum:</h3> 
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=Artist,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

<h3>🔻  Criar um index global secundário baseado no título da música e no ano:</h3> 
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

<h3>🔻 Pesquisar item por artista:</h3> 
```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"Iron Maiden"}}'
```

<h3>🔻 Pesquisar item por artista e título da música:</h3> 
```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values file://keyconditions.json
```

<h3>🔻 Pesquisa pelo index secundário baseado no título do álbum:</h3> 
```
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Fear of the Dark"}}'
```

<h3>🔻 Pesquisa pelo index secundário baseado no nome do artista e no título do álbum:</h3> 
```
aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Iron Maiden"},":v_title":{"S":"Fear of the Dark"} }'
```

<h3>🔻 Pesquisa pelo index secundário baseado no título da música e no ano:</h3> 
```
aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Wasting Love"},":v_year":{"S":"1992"} }'
```
