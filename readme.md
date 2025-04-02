# Traffband Cloudflare Cache Purge for Super Cache

## TODO

### Suggested Security Enhancements:

1. **Add Data Validation:**
   - Validate the API key format and email format before saving
2. **Use Transients for API Results:**
   - Cache API call results temporarily to reduce the risk of API rate limiting
3. **Implement Rate Limiting:**
   - Add internal rate limiting for the manual purge function
4. **Improve Credential Storage:**
   - Consider encrypting the API key in the database (though WordPress itself doesn't do this for API keys by default)
5. **Add Confirmation for Manual Purge:**
   - Implement a confirmation dialog for the manual purge operation
6. Add plugin to own git repository
7. Add tags and Updater Class
