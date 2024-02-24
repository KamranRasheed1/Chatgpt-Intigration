# Chatgpt-Intigration
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

class ChatGPTScreen extends StatefulWidget {
  @override
  _ChatGPTScreenState createState() => _ChatGPTScreenState();
}

class _ChatGPTScreenState extends State<ChatGPTScreen> {
  TextEditingController _messageController = TextEditingController();
  String _response = '';

  Future<void> _sendMessage() async {
    String message = _messageController.text;
    if (message.isNotEmpty) {
      // Replace 'YOUR_API_KEY' with your actual ChatGPT API key
      String apiKey = 'YOUR_API_KEY';
      String endpoint = 'https://api.openai.com/v1/completions';

      Map<String, String> headers = {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer $apiKey',
      };

      Map<String, dynamic> data = {
        'prompt': message,
        'max_tokens': 50, // Adjust max tokens as needed
        'temperature': 0.7, // Adjust temperature as needed
        'n': 1
      };

      var response = await http.post(
        Uri.parse(endpoint),
        headers: headers,
        body: json.encode(data),
      );

      if (response.statusCode == 200) {
        Map<String, dynamic> responseData = json.decode(response.body);
        setState(() {
          _response = responseData['choices'][0]['text'];
        });
      } else {
        setState(() {
          _response = 'Error: ${response.statusCode}';
        });
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('ChatGPT Integration'),
      ),
      body: Padding(
        padding: EdgeInsets.all(20.0),
        child: Column(
          children: [
            TextField(
              controller: _messageController,
              decoration: InputDecoration(labelText: 'Enter your message'),
            ),
            SizedBox(height: 20.0),
            ElevatedButton(
              onPressed: _sendMessage,
              child: Text('Send'),
            ),
            SizedBox(height: 20.0),
            Text(
              'Response:',
              style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 10.0),
            Expanded(
              child: SingleChildScrollView(
                child: Text(
                  _response,
                  style: TextStyle(fontSize: 16),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

void main() {
  runApp(MaterialApp(
    home: ChatGPTScreen(),
  ));
}

