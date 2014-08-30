var express = require('express');
var app = express();

var redis = require('redis').createClient();

function generateNoteID() {
  return [
    Math.random().toString(36).substring(2),
    Math.random().toString(36).substring(2)
  ].join('');
};

var bodyparser = require('body-parser');
var morgan = require('morgan');

app.use('/static', express.static('./'));
app.use(bodyparser.urlencoded());
app.use(morgan('combined'))

app.get('/', function(req, res) {
  res.sendfile('index.html');
});

app.post('/addnote', function(req, res) {
  var content = req.body.content;

  if (typeof content !== 'string') {
    return res.end(400, 'Note content must be a string');
  }

  if (content.length < 3) {
    return res.end(400, 'Note is insufficiently long');
  }

  var id = generateNoteID();

  console.log('ADDING NOTE', id, content);

  redis.set(id, content, function(err) {
    if (err) {
      res.end(500, 'Could not create note, soz');
    } else {
      res.redirect('/note/' + id);
    }
  });
});

app.get('/note/:id', function(req, res) {
  var id = req.params.id;

  console.log('REMOVING NOTE', id);

  redis.multi()
  .get(id)
  .del(id)
  .exec(function(err, content) {
    if (err || !Array.isArray(content) || content.length < 2) {
      res.end(404);
    } else {
      res.end(content[0]);
    }
  });
});

app.listen(8080);
