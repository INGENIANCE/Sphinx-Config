Role Name
=========

This role can be used to generate a Sphinx Search configuration file. The source used as base for this role
can be found here: https://github.com/sphinxsearch/sphinx/blob/master/sphinx.conf.in

Requirements
------------

No requirements.

Role Variables
--------------

Variable name: variable type - description

**output_file_path:** _String_ - Specify where to generate the SphinxSE configuration file.  
**sources:** _List of Sub-dictionaries_ - Each sub-dictionary in this list contains the source parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-source). All values are Strings and the key `name` defines the mandatory name of the source.  
**inherited_sources:** _List of Sub-dictionaries_ - Each sub-dictionary in this list contains the inherited source parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-source). All values are Strings and the keys `name` and `parent` defines the mandatory name and parent's name of the inherited source.  
**indices:** _List of Sub-dictionaries_ - Each sub-dictionary in this list contains the index parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-index). All values are Strings and the key `name` defines the mandatory name of the index.  
**inherited_indices:** _List of Sub-dictionaries_ - Each sub-dictionary in this list contains the inherited index parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-index). All values are Strings and the keys `name` and `parent` defines the mandatory name and parent's name of the inherited index.  
**indexer:** _Sub-dictionary_ - Sub-dictionary containing the indexer parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-indexer). All values are Strings.  
**searchd:** _Sub-dictionary_ - Sub-dictionary containing the searchd parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-searchd). All values are Strings.  
**common:** _Sub-dictionary_ - Sub-dictionary containing the common parameters as key/value pairs. Keys are the name of the parameters as described in the [official documentation](http://sphinxsearch.com/docs/current.html#confgroup-common). All values are Strings.  

Dependencies
------------

None

Example Playbook
----------------

The following playbook generate a sphinx.conf config file to the relative sub-folder sphinx-config:

    - hosts: servers
      var:
          output_file_path: '../sphinx-config/'
          sources:
            - name: 'src1'
              type: 'mysql'
              sql_host: 'localhost'
              sql_user: 'test'
              sql_pass: ''
              sql_db: 'test'
              sql_port: '3306'
              sql_sock: '/tmp/mysql.sock'
              mysql_connect_flags: '32'
              mysql_ssl_cert: '/etc/ssl/client-cert.pem'
              mysql_ssl_key: '/etc/ssl/client-key.pem'
              mysql_ssl_ca: '/etc/ssl/cacert.pem'
              sql_query: '\
            SELECT id, group_id, UNIX_TIMESTAMP(date_added) AS date_added, title, content \
            FROM documents'
              sql_query_pre:
                - 'SET NAMES utf8'
                - 'SET SESSION query_cache_type=OFF'
              sql_joined_field:
                - "tags from query; SELECT docid, CONCAT(\'tag\',tagid) FROM tags ORDER BY docid ASC"
                - 'sql_joined_field	= wtags from payload-query; SELECT docid, tag, tagweight FROM tags ORDER BY docid ASC'
              sql_file_field: 'content_file_path'
              sql_query_range: 'SELECT MIN(id),MAX(id) FROM documents'
              sql_range_step: '1000'
              sql_attr_uint:
                - 'author_id'
                - 'forum_id:9'
                - 'group_id'
              sql_attr_bool:
                - 'is_deleted'
              sql_attr_bigint:
                - 'my_bigint_id'
              sql_attr_timestamp:
                - 'posted_ts'
                - 'last_edited_ts'
                - 'date_added'
              sql_attr_float:
                - 'lat_radians'
                - 'long_radians'
              sql_attr_multi:
                - 'uint tag from query; SELECT docid, tagid FROM tags'
                - 'uint tag from ranged-query; \
            SELECT docid, tagid FROM tags WHERE id>=$start AND id<=$end; \
            SELECT MIN(docid), MAX(docid) FROM tags'
              sql_attr_string:
                - 'stitle'
              sql_attr_json:
                - 'properties'
              sql_field_string:
                - 'author'
              sql_query_post: ''
              sql_query_post_index: "REPLACE INTO counters ( id, val ) \
            VALUES ( 'max_indexed_id', $maxid )"
              sql_ranged_throttle: '0'
              sql_query_killlist: 'SELECT id FROM documents WHERE edited>=@last_reindex'
          indices:
            - name: 'test1'
              type: 'plain'
              source: 'src1'
              path: '@CONFDIR@/data/test1'
              docinfo: 'extern'
              dict: 'keywords'
              mlock: '0'
              morphology: 'stem_en, stem_ru, soundex'
              min_stemming_len: '1'
              stopwords: '@CONFDIR@/data/stopwords.txt'
              wordforms: '@CONFDIR@/data/wordforms.txt'
              exceptions: '@CONFDIR@/data/exceptions.txt'
              embedded_limit: '16K'
              min_word_len: '1'
              ignore_chars: 'U+00AD'
              min_prefix_len: '0'
              min_infix_len: '0'
              max_substring_len: '8'
              prefix_fields:
                - 'filename'
              infix_fields:
                - 'url, domain'
              expand_keywords: '1'
              ngram_len: '1'
              ngram_chars: 'U+3000..U+2FA1F'
              phrase_boundary: '., ?, !, U+2026'
              phrase_boundary_step: '100'
              blend_chars: '+, &, U+23'
              blend_mode: 'trim_tail, skip_pure'
              html_strip: '0'
              html_index_attrs: 'img=alt,title; a=title;'
              html_remove_elements: 'style, script'
              preopen: '1'
              inplace_enable: '1'
              inplace_hit_gap: '0'
              inplace_docinfo_gap: '0'
              inplace_reloc_factor: '0.1'
              inplace_write_factor: '0.1'
              index_exact_words: '1'
              overshort_step: '1'
              stopword_step: '1'
              hitless_words: 'all'
              index_sp: '1'
              index_zones: 'title, h*, th'
              index_field_lengths: '1'
              regexp_filter:
                - '\b(\d+)\" => \1inch'
                - '(blue|red) => color'
              bigram_freq_words: 'the, a, i, you, my'
              bigram_index: 'both_freq'
              stopwords_unstemmed: '0'
              global_idf: '/usr/local/sphinx/var/global.idf'
            - name: 'dist1'
              type: 'distributed'
              local:
                - 'test1'
                - 'test1stemmed'
              agent:
                - 'localhost:9313:remote1'
                - 'localhost:9314:remote2,remote3'
                - '/var/run/searchd.sock:remote4'
                - 'server3:9312 | server4:9312 :indexchunk2'
                - 'server3:9312:chunk2server3 | server4:9312:chunk2server4'
                - 'server3:chunk2server3 | server4:chunk2server4'
                - 'server21|server22|server23:chunk2'
              agent_blackhole:
                - 'testbox:9312:testindex1,testindex2'
              agent_persistent:
                - 'testbox:9312:testindex1,testindex2'
              agent_connect_timeout: '1000'
              agent_query_timeout: '3000'
              ha_strategy: 'nodeads'
              rlp_context: '/usr/local/share/sphinx/rlp/rlp-context.xml'
            - name: 'rt'
              type: 'rt'
              path: '@CONFDIR@/data/rt'
              rt_mem_limit: '512M'
              rt_field:
                - 'title'
                - 'content'
              rt_attr_uint:
                - 'gid'
              rt_attr_bigint:
                - 'guid'
              rt_attr_float:
                - 'gpa'
              rt_attr_timestamp:
                - 'ts_added'
              rt_attr_string:
                - 'author'
              rt_attr_multi:
                - 'tags'
              rt_attr_multi_64:
                - 'tags64'
              rt_attr_json:
                - 'extra_data'
          inherited_indices:
            - name: 'test1stemmed'
              parent: 'test1'
              path: '@CONFDIR@/data/test1stemmed'
              morphology: 'stem_en'
          indexer:
            mem_limit: '128M'
            max_iops: '40'
            max_iosize: '1048576'
            max_xmlpipe2_field: '4M'
            write_buffer: '1M'
            max_file_field_buffer: '32M'
            on_file_field_error: 'skip_document'
            lemmatizer_cache: '512M'
          searchd:
            listen:
              - '127.0.0.1'
              - '192.168.0.1:9312'
              - '9312'
              - '/var/run/searchd.sock'
              - '9312'
              - '9306:mysql41'
            log: '@CONFDIR@/log/searchd.log'
            query_log: '@CONFDIR@/log/query.log'
            read_timeout: '5'
            client_timeout: '300'
            max_children: '30'
            persistent_connections_limit: '30'
            pid_file: '@CONFDIR@/log/searchd.pid'
            seamless_rotate: '1'
            preopen_indexes: '1'
            unlink_old: '1'
            attr_flush_period: '900'
            mva_updates_pool: '1M'
            max_packet_size: '8M'
            max_filters: '256'
            max_filter_values: '4096'
            listen_backlog: '5'
            read_buffer: '256K'
            read_unhinted: '32K'
            max_batch_queries: '32'
            subtree_docs_cache: '4M'
            subtree_hits_cache: '8M'
            workers: 'threads'
            dist_threads: '4'
            binlog_path: '@CONFDIR@/data'
            binlog_flush: '2'
            binlog_max_log_size: '256M'
            thread_stack: '128K'
            expansion_limit: '1000'
            rt_flush_period: '900'
            query_log_format: 'sphinxql'
            mysql_version_string: '5.0.37'
            collation_server: 'utf8_general_ci'
            collation_libc_locale: 'ru_RU.UTF-8'
            watchdog: '1'
            predicted_time_costs: 'doc=64, hit=48, skip=2048, match=64'
            sphinxql_state: 'sphinxvars.sql'
            rt_merge_iops: '40'
            rt_merge_maxiosize: '1M'
            ha_ping_interval: '0'
            ha_period_karma: '60'
            prefork_rotation_throttle: '100'
            snippets_file_prefix: '/mnt/common/server1/'
          common:
            lemmatizer_base: '/usr/local/share/sphinx/dicts'
            on_json_attr_error: 'fail_index'
            json_autoconv_numbers: '1'
            json_autoconv_keynames: 'lowercase'
            rlp_root: '/usr/local/share/sphinx/rlp'
            rlp_environment: '/usr/local/share/sphinx/rlp/rlp/etc/rlp-environment.xml'
            rlp_max_batch_size: '100k'
            rlp_max_batch_docs: '100'
            plugin_dir: '/usr/local/sphinx/lib'
      roles:
         - role: sphinx-config

License
-------

GPLv2

Author Information
------------------

email: afontaine@ingeniance.fr