Documentation
=============
  //Create a stream for the file
                    Stream stream = null;

                    //This controls how many bytes to read at a time and send to the client
                    int bytesToRead = 10000;

                    // Buffer to read bytes in chunk size specified above
                    byte[] buffer = new Byte[bytesToRead];

                    // The number of bytes read
                    try
                    {
                        //Create a WebRequest to get the file
                        HttpWebRequest fileReq = (HttpWebRequest)HttpWebRequest.Create(filePath);

                        //Create a response for this request
                        HttpWebResponse fileResp = (HttpWebResponse)fileReq.GetResponse();

                        if (fileReq.ContentLength > 0)
                            fileResp.ContentLength = fileReq.ContentLength;

                        //Get the Stream returned from the response
                        stream = fileResp.GetResponseStream();

                        
                        //Indicate the type of data being sent
                        Response.ContentType = "application/octet-stream";

                        //Name the file 
                        Response.AddHeader("Content-Disposition", "attachment; filename=\"" + record.Url + "\"");
                        Response.AddHeader("Content-Length", fileResp.ContentLength.ToString());

                        int length;
                        do
                        {
                            // Verify that the client is connected.
                            if (Response.IsClientConnected)
                            {
                                // Read data into the buffer.
                                length = stream.Read(buffer, 0, bytesToRead);

                                // and write it out to the response's output stream
                                Response.OutputStream.Write(buffer, 0, length);

                                // Flush the data
                                Response.Flush();

                                //Clear the buffer
                                buffer = new Byte[bytesToRead];
                            }
                            else
                            {
                                // cancel the download if client has disconnected
                                length = -1;
                            }
                        } while (length > 0); //Repeat until no data is read
                    }
                    finally
                    {
                        if (stream != null)
                        {
                            //Close the input stream
                            stream.Close();
                        }
                    }
