const fs = require('fs');
const { promisify } = require('util');
const { pipeline } = require('stream');

const readFile = promisify(fs.readFile);
const pipelineAsync = promisify(pipeline);

async function parallelGrep(files, pattern) {
 const results = [];

 const promises = files.map(async (file) => {
  try {
   const data = await readFile(file, 'utf-8');
   const lines = data.split('\n');
   const matches = lines.filter(line => line.includes(pattern));

   results.push({
    file: file,
    matches: matches,
   });
  } catch (error) {
   console.error(`Error reading file ${file}: ${error}`);
  }
 });

 await Promise.all(promises);

 return results;
}

// Example usage
const files = ['file1.txt', 'file2.txt', 'file3.txt'];
const pattern = 'keyword';

parallelGrep(files, pattern)
 .then(results => {
  results.forEach(result => {
   console.log(`File: ${result.file}`);
   result.matches.forEach(match => {
    console.log(` ${match}`);
   });
  });
 })
 .catch(error => {
  console.error('Error:', error);
 });
