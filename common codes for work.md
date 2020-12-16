#### export pipeline (typescript)

	const fs = require('fs')
	require('fs').writeFile('message.txt', JSON.stringify((pipeline as any)._pipeline, null, 4), (err:any) => {
            if (err) console.log(err);
            console.log('The file has been saved!');
        });

#### res send html

	res.send(require(‘ejs’).renderFile( require(‘path’).join( __dirname, ‘..’, ‘public’, ‘error’, ‘updateBrowser.ejs’)))
	res.sendFile(require(‘path’).join( __dirname, ‘..’, ‘public’, ‘error’, ‘updateBrowser.html’))
	
### backup and restore data

#### check info (in commend) 
	docker ps

#### backup 
	docker exec etomon-mongo  mongodump --db etomon --archive | xz -9 > backup.xz

#### restore mongo (文件需已解压缩) 
	docker exec -i etomon-mongo mongorestore --archive --drop < ./backup

#### clear redis 
	docker exec -it etomon-redis redis-cli flushdb
	docker exec etomon-redis redis-cli flushdb

